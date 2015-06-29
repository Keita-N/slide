# 関数型プログラミング

## 純粋関数とは
- 同じ引数に対して常に同じ値がかえってくる
- プログラム中のコンテクストに依存しない
- 副作用(side effect)を生じない


- 参照透過性 referential transparency
```
式e があり、すべてのプログラムpにおいて、pの意味に影響を与えることなく、
p内のすべてのeをeの評価結果で置き換えることができるとしたら、eは参照透過である。

関数f があり、式f(x) が参照透過なすべてのx に対して参照透過であるとしたら
f は純粋関数である

An expression e is referential transparency if
for all program p every occurrence of e in p
can be replaced with the result of evaluating e
without changing the meaning of p.

A function f is pure
if f(x) is RT when x is RT.
```
## 関数型プログラミングのメリット
隠れた依存関係がない
初期化、クリーンアップがない　可変の状態を使わない
意図しない副作用がない

Modularity
### Reusable
### Testable
関数の評価結果がコンテクストに依存しないためテストがしやすい
### Parallelizable
one is all, all is one

The whole is the same as the parts

全体と構成要素の構造が同じものとして扱える構造を利用することで、
並列・並行処理をより簡潔、宣言的に定義できる

ex)HTMLパーサー String => HTML
 1. Input をN個のチャンクに分割する
 2. それぞれのチャンクについて同時にパースをかける (String) => (Stirng, HTML, String)
 3. 結果をマージする ((String, HTML, String), (String, HTML, String)) => (String, HTML, String)
 

### Comprehensible
副作用がなく、宣言的な定義ができるので形式的に証明しやすい

```scala
def sum(xs: List[Int]): Int = xs match {
    case Nil => 0
    case h :: t => h + sum(t)
}

def product(xs: List[Int]): Int = xs match {
    case Nil => 1
    case h :: t => h * sum(t)
}
```

```scala
def foldRight[A, B](xs: List[A])(z: B)(f: (A, B) => B): B = xs match {
    case Nil => z
    case h :: t => f(h, foldRight(t))
}

def sum(xs: List[Int]) = foldRight(xs)(0)(_ + _)

def product(xs: List[Int]) = foldRight(xs)(1)(_ * _)

def all(xs: List[Boolean) = foldRight(xs)(true)(_ && _)

def any(xs: List[Boolean]) = foldRight(xs)(false)(_ || _)

def concat(xs: List[String]) = foldRight(xs)("")(_ + _)
```

## FP => Happiness
詳細は、以下の動画で。

## 参考（元ネタ）
- 「Scala関数型デザイン＆プログラミング　―scalazコントリビューターによる関数型徹底ガイド」
- [Lambda Days 2015 - Rúnar Bjarnason - An invitation to functional programming](https://www.youtube.com/watch?v=aa7jh1J4xNs)
