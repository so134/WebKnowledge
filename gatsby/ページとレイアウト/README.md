# ページとレイアウトについて

## プロジェクト構造
```
root
├─/.cache
├─/plugins
├─/public
├─/src
├─/pages
├─/templates
├─html.js
├─/static
├─gatsby-config.js
├─gatsby-node.js
├─gatsby-ssr.js
└─gatsby-browser.js
```

## gatsby-xxxx.jsについて

### gatsby-config.js

おもにプラグインの設定、サイトのメタデータやタイトル等の設定を行う.
module export はここで行う

```js
module.exports = {
  siteMetadata: {
    title: `Gatsby`,
  },
  plugins: [
    `gatsby-transform-plugin`,
    {
      resolve: `gatsby-plugin-name`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```

- 構成要素に含まれるもの

1. siteMetadata

サイト全体で共通のデータ（サイトのタイトルなど）を再利用する場合は、そのデータをsiteMetadataに保存可能

```js
module.exports = {
  siteMetadata: {
    title: `Gatsby`,
    siteUrl: `https://www.gatsbyjs.com`,
    description: `Blazing fast modern site generator for React`,
  },
}
```


2. plugins

GatsbyApiを実装する、Node.jsパッケージ群

npmなどのパッケージマネージャーを使用してプラグインをインストールしても、Gatsbyサイトでプラグインを有効にすることはできない。
プラグインを追加するには、gatsby-config.jsファイルにplugins配列が含まれている必要がある

```js
module.exports = {
  plugins: [
    //plugins go here
  ],
}
```
optionが必要ない場合
```js
module.exports = {
  plugins: [`gatsby-plugin-name`],
}
```
optionを必要とする場合
```js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-name`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```
plugin内で別のpluginを利用することも可能
```js
module.exports = {
  plugins: [
    `gatsby-transform-plugin`,
    {
      resolve: `gatsby-plugin-name`,
      options: {
        optionA: true,
        optionB: `Another option`,
      },
    },
  ],
}
```


3. flags

フラグを使用すると、まだテスト中または次のメジャーリリースを待っている実験的なもの、または今後の変更を有効にすることが可能。

```js
module.exports = {
  flags: {
    QUERY_ON_DEMAND: true,
  },
}
```


4. pathPrefix

ドメインのルート以外の場所でサイトがホストされる場合に利用する。

example.com/blog/にGatsbyサイトがある場合、サイトのすべてのパスにプレフィックス（/ blog）を追加する必要がある

```js
module.exports = {
  pathPrefix: `/blog`,
}
```

5. trailingSlash

- always: 各URLには常に末尾のスラッシュを追加 例） /x to /x/
- never: 各URLの末尾のスラッシュをすべて削除 例） /x/ to /x
- ignore: 自動的に変更させない

Gatsby v4 まではデフォルトで `legacy` に設定されるが、Gatsby v5 ではデフォルトで `always` となるので注意

6. polyfill

GatsbyはES6PromiseAPIを使用している。一部のブラウザはこれをサポートしていないため、GatsbyにはデフォルトでPromiseポリフィルが含まれている。
独自のPromiseポリフィルを提供する場合は、ポリフィルをfalseに設定する

```js
module.exports = {
  polyfill: false,
}
```

7. Proxy

プロキシ設定オプションを設定すると、開発サーバーに不明なリクエストを指定した場合、サーバーにプロキシする

```js
module.exports = {
  proxy: {
    prefix: "/api",
    url: "http://examplesite.com/api/",
  },
}
```

8. jsxRuntime

`automatic` に設定すると、ReactをインポートしなくてもJSXを使用できる

```js
module.exports = {
  jsxRuntime: "automatic",
}
```
