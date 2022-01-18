# useSWR について

## 何者？

[SWR](https://github.com/vercel/swr)はVercel製のライブラリ

### SWRとは  [stale-while-revalidate](https://web.dev/stale-while-revalidate/)

SWRとは、

> キャッシュからデータを返し（stale）、次にフェッチリクエストを送り（revalidate）、最後に最新のデータを持ってくる

という戦略

useSWRというReact Hooksを用いることで、APIを通じたデータの取得・キャッシュを簡単に記述できる。


## 概要

```tsx
import useSWR from 'swr'

function Profile() {
  // key と fetcher　を受け取る
  const { data, error } = useSWR('/api/user', fetcher)

  // 失敗
  if (error) return (<div>failed to load</div>)
  // 通信中
  if (!data) return (<div>loading...</div>)
  // 完了
  return (<div>hello {data.name}!</div>)
}
```

useSWR フックは key 文字列と fetcher 関数を受け取る。
key はデータの一意な識別子でAPIの requestURL 等を使用するのが一般的

key は fetcher に渡される。 fetcher はデータを返す任意の非同期関数

このフックは、リクエストの状態にもとづいて data と error の二つの値を返す。


## 特徴

- 速い、 軽量 そして 再利用可能 なデータの取得
- 組み込みの キャッシュ とリクエストの重複排除
- リアルタイム な体験
- トランスポートとプロトコルにとらわれない
- SSR / ISR / SSG support
- TypeScript 対応
- React Native

## 使い方

key はデータの一意な識別子（通常は API の URL）で、fetcher に渡される。
fetcher はデータを返す任意の非同期関数
keyに渡したパスに対してGETが走る

### パラメータ

- `key` : このリクエストのためのユニークなキー文字列（または関数、配列、null）
- `fetcher` : （任意） データをフェッチするための Promise を返す関数
- `options` : （任意） この SWR フックのオプションオブジェクト https://swr.vercel.app/ja/docs/options

### 返り値

- `data` : fetcher によって解決された、指定されたキーのデータ（もしくは、ロードされていない場合は undefined）
- `error` : fetcher によって投げられたエラー （もしくは undefined）
- `isValidating` : リクエストまたは再検証の読み込みがあるかどうか。errorとdata のみでloadingを表現したくない場合、エラーが帰ってきているが、再試行しその検証中はloadingにしたい。などの場合。
- `mutate(data?, shouldRevalidate?)` : キャッシュされたデータを更新する関数

### 再利用可能に

同じデータを複数の場所で扱いたい場合など、単純にuseSWRを直接記述するだけでは不便なので、再利用可能な形に切り出して使うことが推奨されている。（コードの重複を防ぐ）

```js
function useUser (id) {
const { data, error } = useSWR(`/api/user/${id}`, fetcher)
return {
    user: data,
    isLoading: !error && !data,
    isError: error
  }
}
```
使う側
```js
function Avatar ({ id }) {
	const { user, isLoading, isError } = useUser(id)

	if (isLoading)return <Spinner />
	if (isError)return <Error />

	return <img src={user.avatar} />
}
```

## Revalidateのタイミング

### Revalidate on Focus

フォーカス時の再検証
ページを再フォーカスしたり、タブを切り替えたりすると、SWRは自動的にデータを再検証する

### Revalidate on Interval

intervalでの検証の仕組み

Q : 常に裏で再検証されていたら、何回もAPIリクエストが発生するのでは？

A : フックに関連付けられたコンポーネントが画面に表示されている場合にのみ再取得が行われる

感想 : すごい

```js
useSWR(`/api/user/${id}`, fetcher, { refreshInterval: 1000 })
```

### Revalidate on Reconnect

ネットワークが切断されて再度接続された場合に再検証される

### 手動での再検証

mutate(key)を呼ぶことで、同じキーを持つ全てのキャッシュに対してrevalidateをかけることが可能

```js
// １回目
const { data, error } = useSWR(`/api/user/`, fetcher)

// 手動でrevalidate
mutate('/api/user')
```


## POSTする

### Mutation and POST Request

POSTとmutateを組み合わせて使う
postした段階でローカルのキャッシュを書き換えつつ、postリクエストを送信。
その後データの再検証を行う。
→ エンドユーザーはpostの結果を待つことなく更新後のデータが表示される

```js
import useSWR, { mutate } from 'swr'

function Profile () {
  const { data } = useSWR('/api/user', fetcher)

  return (
    <div>
      <h1>My name is {data.name}.</h1>
      <button onClick={async () => {
        const newName = data.name.toUpperCase()
        // update the local data immediately, but disable the revalidation
        mutate('/api/user', { ...data, name: newName }, false)
        // send a request to the API to update the source
        await requestUpdateUsername(newName)
        // trigger a revalidation (refetch) to make sure our local data is correct
        mutate('/api/user')
      }}>Uppercase my name!</button>
    </div>
  )
}
```

## 感想

便利ですごいなぁ

