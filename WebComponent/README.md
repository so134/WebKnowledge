# WebComponents について

## 概念

**可能な限りコードを再利用したい**

### WebComponents の持つ 3 つの主要な技術

- [Custom Elements](.CustomElementss)
- [Shadow DOM](.ShadowDOM)
- [HTML Templates](.HTMLTemplates)

<a class="CustomElements"></a>

## Custom Elements

### TL;DR

- `<div>`の代わりに`<my-element>`というように独自の命名のタグを生成できる
- HTML × JavaScript (JSX)の形
- 場合によっては[Polyfill](.Polyfill)が必要
- 長い[MutationObserver](.MutationObserver)との決別

### モダンな HTMl

1. div 依存

   Q. なぜ div を多用する？

   A. 他にいい感じのタグがないから仕方なく使ってる

   Authors are strongly encouraged to view the div element as an element of last resort, for when no other element is suitable.[W3C HTML5.1：The div element](https://www.w3.org/TR/2016/REC-html51-20161101/grouping-content.html#the-div-element)

   既存の HTMl では、div 意外に適切な解決策がない

   そこで、CustomElements を用いて、適切なマークアップを行うようにする

   ```html
   <div class="user__profile">
     <div class="layout card small">...</div>
   </div>
   <user-profile>
     <card-layout type="small">...</card-layout>
   </user-profile>
   ```

2.

<a class="ShadowDOM"></a>

## ShadowDOM

通常の DOM ツリーの要素の下に DOM ツリーを追加し隠すことが可能。

shadow DOM ツリーは shadow root を根とし、その下には普通の DOM ツリーと同様に任意の要素を追加可能。

<a class="HTMLTemplates"></a>

## HTML テンプレート

## 語彙

<a class="Polyfill"></a>

- Polyfill

  例として、iOS の Safari であれば、ＯＳのバージョンアップをしない限りは自動でバージョンが上がることはない。

  そのようなブラウザが対応できていない部分を埋めることを「ポリフィル」と呼ぶ。

  <a class="MutationObserver"></a>

- MutationObserver

  DOM の更新時にコールバック関数を実行させる
