# 関数プログラミング用語集

> 本プロジェクトは[functional-programming-jargon](https://github.com/hemanth/functional-programming-jargon)を日本語に翻訳したものとなります。専門用語が多く、誤訳がある可能性がありますので、発見された場合はPRで修正していただきたいです。

関数型プログラミングには多くの利点があり、普及が進んでいる。しかし、プログラミングパラダイムのそれぞれには独自の専門用語があり、関数型プログラミングもその例外ではない。この用語集が、関数型プログラミングの理解の手助けとなれば幸いである。

プログラムの例はJavaScript(ES2015)とする。[なぜJavaScriptを使うのか?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

ここでは、[Fantasy Land spec](https://github.com/fantasyland/fantasy-land)で定義された用語を適宜使用している。

__目次__

- [Arity](#arity)
- [高階関数](#HOF)

## Arity

関数が持つ引数の数。引数が1つの関数を単項(unary)、2つのことを二項(binary)、3つのことを三項(ternary)...という。

```js
// 引数は2つ
const sum = (a, b) => a + b;
// 引数は1つ
const inc = a => a + 1;
// 引数は0
const zero = () => 0;
```

__より詳しく__
- [Arity](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%AA%E3%83%86%E3%82%A3) (Wikipedia)

<div id=HOF />

## 高階関数(Higher-Order Function / HOF)

関数を引数や返り値としてもつ関数。

```js
const filter = (predicate, xs) => xs.filter(predicate);

const is = (type) => (x) => Object(x) instanceof type;

filter(is(Number), [0, '1', 2, null]); // [0, 2]
```
