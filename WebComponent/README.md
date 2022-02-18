# WebComponents について

## 概念

**可能な限りコードを再利用したい**

### WebComponents の持つ 3 つの主要な技術

- [Custom Elements](#CustomElementss)
- [Shadow DOM](#ShadowDOM)
- [HTML Templates](#HTMLTemplates)

<a id="CustomElements"></a>

## Custom Elements

### TL;DR

- `<div>`の代わりに`<my-element>`というように独自の命名のタグを生成できる
- HTML × JavaScript (JSX)の形
- 場合によっては[Polyfill](#Polyfill)が必要
- 長い[MutationObserver](#MutationObserver)との決別

   <br />
   <br />

### モダンな HTML

1.  div 依存

    div だらけの html...

    ```html
    <div class="class1">
      <div class="class2">
        <div class="class3">
          <div class="class4">
            <div class="class5">hoge...</div>
          </div>
        </div>
        <div class="class3">
          <div class="class4">
            <div class="class5">hoge...</div>
          </div>
        </div>
      </div>
    </div>
    ```

    Q. なぜ div を多用する？

    A. 他にいい感じのタグがないから仕方なく使ってる

    <br/>

    > Authors are strongly encouraged to view the div element as an element of last resort, for when no other element is suitable.[W3C HTML5.1：The div element](https://www.w3.org/TR/2016/REC-html51-20161101/grouping-content.html#the-div-element)

    <br/>

    既存の HTMl では、div 意外に適切な解決策がない<br/>
    そこで、CustomElements を用いて、適切なマークアップを行うようにする

    ```html
    <div class="user__profile">
      <div class="layout card small">...</div>
    </div>
    <user-profile>
      <card-layout type="small">...</card-layout>
    </user-profile>
    ```

    <br />

2.  DOM 操作

    `<div>`は js と関連がない。<br/>
    JS が各エレメントを操作して動かしている。

    ```js
    // elementを取得
    constructor(element) {
     this.__element = element;
    }
    ...
    // elementのinnerText
    this.__element.innerText = 'text';
    ...

    document.addEventListener('DOMContentLoaded', () => {
     CurrentTime.create(document.querySelector('.current-time'));
    }, false);

    this.__disconnectedObserver = new MutationObserver(mutations => { ... this.dispose() ... });

    this.__disconnectedObserver.observe(this.__element.parentNode, {
     childList: true
    });
    ```

    - 上記コードでは js が element を取得するまでお互いに何も関係を持たない。

    - 取得後も、element は property に保存して操作する必要がある。

    - 長い MutationObserver コードを作成する必要がある

    ```js
    class CurrentTime extends HTMLElement {...}
    ...
    this.innerText = 'text';
    ...
    // connect
    connectedCallback() { ... }
    // disconnect
    disconnectedCallback() { ... }
    ```

    customElement のコードでは、[クラス](#Class)の[インスタンス](#Instance) `this`が element のインスタンスそのものになる

    **Web アプリケーションを作成するのに必要な HTML の抜けた部分を、自然にかつ直感的な方法で提供可能**

     <br />

3.  命名規則

    文字の中に - を 1 つ以上含める必要がある

    [正しい命名](https://www.w3.org/TR/custom-elements/#valid-custom-element-name)

    ```html
    <my-editor></my-editor>
    <custom-element></custom-element>
    <super-text></super-text>
    ```

    この制約は、html パーサが JS で宣言された customElement とわからない場合でも、customElement **かもしれない**タグを区別するためにある

    [タグ名に基づいて html 要素が割り当てられる](https://html.spec.whatwg.org/multipage/dom.html#elements-in-the-dom)

    <br/>

4.  connectedCallback＆disconnectedCallback

    - `connectedCallback`と`disconnectedCallback`は HTML 要素を継承した場合、customElement が DOM に追加/削除されるたびに実行される。<br/>
      このとき、インスタンスオブジェクトは生成/破壊されないため、DOM を変更することで何回も実行可能。

    - 一度生成されたインスタンスオブジェクトは残り続けるため、`disconnectedCallback`で適切にインスタンスオブジェクトを整理する必要がある。<br/>
      `connectedCallback`は、インスタンスが DOM に追加された後に発火するため、DOM 操作をここで行う。

    - なお、`connectedCallback`発火時点で要素が DOM に追加されているが、子要素にはまだ DOM に追加されていない。<br/>
      したがって、子要素を変更できても、HTML 形式で挿入された子要素にはアクセスできない。

    <br />
    <br />
    <br />

<a id="ShadowDOM"></a>

## ShadowDOM

通常の DOM ツリーの要素の下に DOM ツリーを追加し隠すことが可能。

shadow DOM ツリーは shadow root を根とし、その下には普通の DOM ツリーと同様に任意の要素を追加可能。

   <br />
   <br />
   <br />
<a id="HTMLTemplates"></a>

## HTML テンプレート

   <br />
   <br />
   <br />

## 語彙

<a id="Polyfill"></a>

- Polyfill

  例として、iOS の Safari であれば、ＯＳのバージョンアップをしない限りは自動でバージョンが上がることはない。

  そのようなブラウザが対応できていない部分を埋めることを「ポリフィル」と呼ぶ。

  <a id="MutationObserver"></a>

- MutationObserver

  DOM の更新時にコールバック関数を実行させる

  <a id="Class"></a>

- クラス

  テンプレート、設計図

  ```js
  class クラス名 {
    constructor() {}
  }
  ```

  <a id="Instance"></a>

- インスタンス

  クラス（テンプレート）から作り出したオブジェクト

  ```js
  const インスタンス名(オブジェクト名) = new Actor（クラス名）;
  ```

