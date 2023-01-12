# 関数プログラミング用語集

> 本プロジェクトは[functional-programming-jargon](https://github.com/hemanth/functional-programming-jargon)を日本語に翻訳したものとなります。専門用語が多く、誤訳がある可能性がありますので、発見された場合はPRで修正していただきたいです。

関数型プログラミングには多くの利点があり、普及が進んでいる。しかし、プログラミングパラダイムのそれぞれには独自の専門用語があり、関数型プログラミングもその例外ではない。この用語集が、関数型プログラミングの理解の手助けとなれば幸いである。

プログラムの例はJavaScript(ES2015)とする。[なぜJavaScriptを使うのか?](https://github.com/hemanth/functional-programming-jargon/wiki/Why-JavaScript%3F)

ここでは、[Fantasy Land spec](https://github.com/fantasyland/fantasy-land)で定義された用語を適宜使用している。

__目次__

- [Arity](#arity)
- [高階関数](#HOF)
- [クロージャ](#closure)
- [部分適用](#partialapplication)
- [カリー化](#currying)
- [自動カリー化](#autocurrying)
- [関数の合成](#functioncomposition)
- [継続](#continuation)
- [純粋関数](#purefunction)
- [副作用](#sideeffect)
- [べき等](#idempotent)
- [ポイントフリースタイル](#pointfree)
- [Predicate](#predicate)
- [契約、コントラクト](#contracts)
- [圏](#category)
- [値](#value)
- [定数](#constant)
  - [定数関数](#constantfunction)
  - [定数関手](#constantfunctor)
  - [定数モナド](#constantmonad)
- [関手](#functor)
- [点付き関手](#pointedfunctor)
- [リフト](#lift)

<div id=arity>

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

<div id=HOF>

## 高階関数 (Higher-Order Function / HOF)

関数を引数や返り値としてとる関数。

```js
const filter = (predicate, xs) => xs.filter(predicate);

const is = (type) => (x) => Object(x) instanceof type;

filter(is(Number), [0, '1', 2, null]); // [0, 2]
```

<div id=closure>

## クロージャ (Closure)

クロージャは、関数のローカル変数を補足するスコープであり、定義されたブロックの外で実行された後でも参照できるようにしたものである。これにより、返された関数からクロージャの中にある値を参照することができる。

```js
const addTo = x => y => x + y;
var addToFive = addTo(5);
addToFive(3); // => 8
```

この場合は、`addToFive`のクロージャ内で`x`は値が`5`として保持される。`addToFive`は`y`の値が代入されたときに合計値を返す。

__詳細__
- [ラムダ式 vs クロージャ](https://stackoverflow.com/questions/220658/what-is-the-difference-between-a-closure-and-a-lambda)
- [JavaScriptのクロージャについての議論](https://stackoverflow.com/questions/111102/how-do-javascript-closures-work)

<div id=partialapplication>

## 部分適用 (Partial Application)

部分適用とは、元の関数の引数のいくつかを事前に埋めて、新たな関数を作ることをいう。

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

<div id=currying>

## カリー化 (Currying)

カリー化とは、複数の引数をとる関数を、引数を1つずつとる関数に変換することをいう。

関数が呼び出されるたびに引数を1つだけ受け取り、すべての引数が渡されるまで、1つの引数を取る関数を返す。

```js
const sum = (a, b) => a + b;

const curriedSum = (a) => (b) => a + b;

curriedSum(40)(2); // 42

const add2 = curriedSum(2); // (b) => 2 + b

add2(10); // 12
```

<div id=autocurrying>

## 自動カリー化 (Auto Currying)

自動カリー化とは、複数の引数をとる元の関数を、本来の引数の数より少ない数の引数を渡したときに、残りの引数をとる関数を返す関数に変換することである。

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

<div id=functioncomposition> 

## 関数の合成 (Function Composition)

2つの関数を組み合わせて、1つの関数の出力がもう1つの関数の入力となるような関数をつくること。この考え方は、関数プログラミングの中で最も重要なアイデアの1つである。

```js
const compose = (f, g) => (a) => f(g(a)); // 関数の合成の定義

const floorAndToString = compose((val) => val.toString(), Math.floor); // 使用

floorAmdToString(121.212121); // '121'
```
 
<div id=continuation>
 
## 継続 (Continuation)
 
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

<div id=purefunction>

## 純粋関数 (Pure Function)

関数が、入力された値に対して決定的に値が決まる、また、副作用がないという条件を満たすとき、純粋であるという。純粋関数は、引数が同じ場合には返り値が同じ値にならなければならない。

```js
const greet = (name) => `Hi, %{name}`;

greet('Brianne"); // 'Hi, Brianne'
```

以下の例は純粋関数とは対照的なものとなる。

```js
window.name = 'Brianne';

const greet = () => `Hi, ${window.name}`;

greet(); // 'Hi, Brianne'
```

上の例では、関数の外の値を参照している。

```js
let greeting

const greet = (name) => {
  greeting = `Hi, ${name}`
};

greet('Brianne');
greeting; // 'Hi, Brianne'
```

上の例では、関数の外の状態を設定している。

<div id=sideeffect>

## 副作用 (Side effects)

関数や式が値を返すのとは別に、外にある変更可能な要素と(読み込みや書き込みで)相互作用がある場合、その関数や式は副作用を持つという。

```js
const differentEveryTime = new Date();
```

```js
console.log('IO is a side effect!');
```

<div id=idempotent>

## べき等 (Idempotent)

結果に関数を再び適用しても同じ結果が得られるとき、その関数はべき等であるという。

```js
Math.abs(Math.abs(10));
```

```js
sort(sort(sort([2, 1)));
```

<div id=pointfree>

## ポイントフリースタイル (Point-Free Style)

定義が使う引数を明示的に特定しないという関数の書き方をポイントフリースタイルという。
この書き方をするときには通常、[カリー化](#currying)や他の[高階関数](#HOF)が必要となる。
また、ポイントフリースタイルはTacit Programmingとも呼ばれる。

```js
// 準備
const map = (fn) => (list) => list.map(fn);
const add = (a) => (b) => a + b;

// 以下、ポイントフリーとそうでないものの比較

// ポイントフリーでない関数 - `numbers`は明示的な引数
const incrementAll = (numbers) => map(add(1))(numbers);

// ポイントフリーな関数 - リストは暗黙的な引数
const incrementAll2 = map(add(1));
```

ポイントフリーで書かれた関数定義は、`function`や`=>`なしで通常の代入をするように見える。
ポイントフリーな関数にすることで、複雑になり理解が難しくなるので、必ずしも必要になるというわけではない。


<div id=predicate>

## Predicate

predicateとは、与えられた値に対して真偽値を返す関数である。通常は、配列のfilterのコールバックとして用いられる。

```js
const predicate = (a) => a > 2;

[1, 2, 3, 4].filter(predicate); // [3, 4]
```

<div id=contracts>

## 契約、コントラクト (Contract)

契約は、実行時に関数や式から生じる義務や利益を指定するものである。契約は、関数や式の入出力からルールの集合として振る舞い、契約に違反した場合は、通常、エラーとして報告される。

```js
// 契約の定義 : int -> boolean
const contract = (input) => {
  if (typeof input === 'number') return true;
  throw new Error('Contract violated: expected int -> boolean');
};

const addOne = (num) => contract(num) && num + 1;

addOne(2); // 3
addOne('some string'); // Contract violated: expected int -> boolean
```

<div id=category>

## 圏 (Category)

圏論における圏とは、対象とその間の射からなる構造である。プログラミングでは通常、型が対象、関数が射として扱われる。

圏が成立するためには、以下の3つの条件を満たす必要がある。

1. 対象自身を指す恒等射が存在しなければならない。`a`が圏に存在する場合、`a -> a`となる関数が存在しなければならない。
2. 射が構成されてなければいけない。圏に元`a`、`b`、`c`、`a -> b`を示す射`f`、`b -> c`を示す射`g`が存在し、`g(f(x))`は`(g • f)(x)`と等価でなければならない。
3. `f • (g • h)`と`(f • g) • h`が等価であるように、合成は結合則を満たさなければならない。

これらのルールは、非常に抽象的なレベルで合成を支配しているので、圏論は新しい合成の方法を明らかにするのに適している。

例として、圏MAXをクラスとして定義する。

```js
class Max {
  constructor (a) {
    this.a = a;
  };
  id () {
    return this;
  };
  compose (b) {
    return this.a > b.a ? this : b;
  };
  toString () {
    return `Max(${this.a})`;
  };
};

new Max(2).compose(new Max(3)).compose(new Max(5)).id().id(); // => Max(5)
```

__詳細__

- [Category Theory for Programmers](https://bartoszmilewski.com/2014/10/28/category-theory-for-programmers-the-preface/)

<div id=value>

## 値 (Value)

変数に代入できるもの。

```js
5
Object.freeze({name: 'John', age: 30}); // `freeze`関数はイミュータブルを強制する
;(a) => a;
;[1];
undefined
```

<div id=constant>

## 定数 (Constant)

一度定義すると変えることができない変数。

```js
const five = 5;
const john = Object.freeze({name: 'John', age: 30});
```

定数は[参照透過性]()を満たす。これは、結果に影響を与えることなく、値を置き換えることができる。

2つの定数を含む下の式では、常に`true`を返す。

```js
john.age + five === ({name: 'John', age: 30}).age + (5);
```

<div id=constantfunction>

### 定数関数 (Constant Function)

2つめの引数を無視した[カリー化](#currying)関数。

```js
const constant = a => () => a;

;[1, 2].map(constant(0)); // => [0, 0]
```

<div id=constantfunctor>

### 定数関手 (Constant Functor)

`map`が内容を変換しないオブジェクト。詳しくは[関手](#functor)を参照。

```js
Constant(1).map(n => n + 1); // => Constant(1)
```

<div id=constantmonad>

### 定数モナド (Constant Monad)

`chain`が内容を変更しないオブジェクト。詳しくは[関手](#functor)を参照。

<div id=functor>

## 関手 (Functor)

そのオブジェクトの内容に関数を持つ`map`関数を含むオブジェクト。関手は以下の2つの規則に従わなければならない。

__恒等射の保存__

```js
object.map(x => x);
```

これは`object`に相当する。

__合成可能__

```js
object.map(f).map(g);
```

(`f`,`g`は任意の合成可能な関数)

[オプション](#option)の参照実装は規則を満足するので関手である。

```js
some.(1).map(x => x); // = some(1)
```

かつ

```js
const f = x => x + 1;
const g = x => x * 2;

some(1).map(x => g(f(x))); // = some(3)
some(1).map(f).map(g); // = some(3)
```

<div id=pointedfunctor>

## 点付き関手(Pointed Functor)

任意の1つの値を入れることができる`of`関数をもつオブジェクト。

ES2015で`Array.of`が追加され、配列を点付き関手にできるようになった。

```js
Array.of(1); // 1
```

<div id=lift>

## リフト(Lift)

リフトとは、ある値をとって[関手](#functor)のようなオブジェクトに入れることをいう。関数を[応用関手](#applicativefunctor)にリフトするとき、関手に含まれる値に対しても作用させることができる。

いくつかの実装においては、関手での関数の実行を簡単にするため、`lift`や`liftA2`という関数が用意されている。

```js
const liftA2 = (f) => (a, b) => a.map(f).ap(b); // `map`ではなく`ap`であることに注意

const mult = a => b => a * b;

const liftedMult = liftA2(mult); // この関数は関手上で配列のようにはたらく

liftedMult([1, 2], [3]); // [3, 6]
liftA2(a => b => a + b)([1, 2], [3, 4]); // [4, 5, 5, 6]
```

1つの引数をもつ関数をリフトすることで、`map`と同じことをすることができる。

```js
const increment = (x) => x + 1;

lift(increment)([2]); // [3]
;[2].map(increment); // [3]
```

単純な値をリフトすることは、オブジェクトを作成することになりうる。

```js
Array.of(1); => [1]
```