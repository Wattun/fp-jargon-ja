# 関数プログラミング用語集

> 本プロジェクトは[functional-programming-jargon](https://github.com/hemanth/functional-programming-jargon)を日本語に翻訳したものとなります。専門用語が多く、誤訳がある可能性がありますので、発見された場合はPRで修正していただきたいです。

関数型プログラミングには多くの利点があり、普及が進んでいる。しかし、プログラミングパラダイムのそれぞれには独自の専門用語があり、関数型プログラミングもその例外ではない。この用語集が、関数型プログラミングの理解の手助けとなれば幸いである。

プログラムの例はJavaScript(ES2015)とする。[なぜJavaScriptを使うのか?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

ここでは、[Fantasy Land spec](https://github.com/fantasyland/fantasy-land)で定義された用語を適宜使用している。

__目次__

- [Arity](#arity)
- [高階関数](#HOF)
- [クロージャ](#closure)
- [カリー化](#currying)
- [関数の合成](#functioncomposition)
- [継続](#continuation)

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

__詳細__
- [Arity](https://ja.wikipedia.org/wiki/%E3%82%A2%E3%83%AA%E3%83%86%E3%82%A3) (Wikipedia)

<div id=HOF />

## 高階関数(Higher-Order Function / HOF)

関数を引数や返り値としてとる関数。

```js
const filter = (predicate, xs) => xs.filter(predicate);

const is = (type) => (x) => Object(x) instanceof type;

filter(is(Number), [0, '1', 2, null]); // [0, 2]
```

<div id=closure />

## クロージャ(Closure)

クロージャは、感ず宇野ローカル変数を補足するスコープであり、定義されたブロックの外で実行された後でも参照できるようにしたものである。これにより、返された関数にからクロージャの中にある値んを参照することができる。

```js
const addTo = x => y => x + y;
var addToFive = addTo(5);
addToFive(3); // => 8
```

この場合は、`addToFive`のクロージャ内で`x`は値が`5`として保持される。`addToFive`は`y`の値が代入されたときに合計値を返す。

__詳細__
- [ラムダ式 vs クロージャ](https://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
- [JavaScriptのクロージャについての議論](https://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

<div id=partialapplication />

## 部分適用(Partial Application)

部分適用とは、元の関数のいくつかを事前に埋めて、新たな関数を作ることをいう。

```js
// 部分適用された関数を作る準備
// 関数と引数をいくつか与える
const partial = (f, ...args) =>
  // 残りの引数をとる関数を返す
  (...moreArgs) => 
    // 与えられた要素をもとの関数に渡す
    f(...args, ...moreArgs)
    
const add3 = (a, b, c) => a + b + c;

// '2'と'3'を与え、残り1つの引数をとる関数を作る
const fivePlus = partial(add3, 2, 3); // (c) => 2 + 3 + c

fivePlus(4); // 9
```

また、JavaScriptの`Function.prototype.bind`を使っても、部分適用を実装できる。
```js
const add1More = add3.bind(null, 2, 3); // (c) => 2 + 3 + c
```

部分適用を使って値を固定することで、複雑な関数からよりシンプルな関数を作ることができる。
後述の[カリー化](#currying)された関数では、自動的に部分適用される。

<div id=currying />

## カリー化(Currying)

カリー化とは、複数の引数をとる関数を、引数を1つずつとる関数に変換することをいう。

関数が呼び出されるたびに引数を1つだけ受け取り、すべての引数が渡されるまで、1つの引数を取る関数を返す。

```js
const sum = (a, b) => a + b;

const curriedSum = (a) => (b) => a + b;

curriedSum(40)(2); // 42

const add2 = curriedSum(2); // (b) => 2 + b

add(10); // 12
```

<div id=autocurrying />

## 自動カリー化(Auto Currying)

自動カリー化とは、複数の引数をとるもとの関数を、正しい引数の数より少ない数の引数を渡したときに、残りの引数をとる関数を返す関数に変換することである。

ライブラリであるlodashとRamdaでは、以下のようなカリー関数が動作する。

```js
const add = (x, y) => x + y;

const curriedAdd = _.curry(add);

curriedAdd(1, 2); // 3
curriedAdd(1); // (y) => 1 + y
curriedAdd(1)(2); // 3
```

__詳細__

- [Favoring Curry](http://fr.umio.us/favoring-curry/)
- [Hey Underscore, You're Doing It Wrong!](https://www.youtube.com/watch?v=m3svKOdZijA)

<div id=functioncomposition /> 

## 関数の合成(Function Composition)

2つの関数を組み合わせて、1つの関数の出力がもう1つの関数の入力となるような関数をつくること。この考え方は、関数プログラミングの中で最も重要なアイデアの1つである。

```js
const compose = (f, g) => (a) => f(g(a)); // 関数の合成の定義

const floorAndToString = compose((val) => val.toString(), Math.floor); // 使用

floorAmdToString(121.212121); // '121'
```
 
<div id=continuation />
 
## 継続(Continuation)
 
プログラムの任意の時点で、まだ実行されていない部分のことを継続という。

```js
const printAsString = (num) => console.log(`Given ${num}`);

const addOneAnfContinue = (num, cc) => {
  const result = num + 1;
  cc(result);
}

addOneAndContinue(2, printAsString); // 'Given 3'
```

継続は、プログラムでデータの受け取りを待機する必要があるときなど、非同期プログラミングでよくみられる。
レスポンスが一度受け取られると、継続であるプログラムの一部に渡されることが多い。

```js
const continueProgramWith = (data) => {
  // データでプログラムを継続する
};

readFileAsync('path/to/file', (err, response) => {
  if (err) {
    // handleエラー
    return;
  }
  continueProgramWith(response);
});
```