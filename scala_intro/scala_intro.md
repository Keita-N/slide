Scala
=====


* オブジェクト指向
* 関数型
* 再利用
* 並列化
* 一般化
* テスト容易性

Scala vs Java
------
## 変数定義

```java
// Java
int i = 2;
```

```scala
// Scala
val i = 2;　// immutable!

var j = 3;
j = 4 // 代入可能
```

## メソッド定義
```java
// Java
public Sting toString(String name, int age) {
  return "name" + " is " + age + "years old.";
}
```
```scala
def toString(name: String, age: Int) =
  s"$name is $age years old."
```
  - return は基本的に書かない。最後に評価された値が戻り値。
  - 一行で書ける場合は、{} 不要
  - 関数はFunction traitを継承した一級オブジェクト（後述）
  - 戻り型は型推論により省略可能
  - s"$i ..."は文字列内で式を展開

## List
```java
// Java
List<String> list = new ArrayList<String>();
list.add("Alice");
list.add("Bob");
list.add("Cecil");
```
```scala
// Scala
val list = List("Alice", "Bob", "Cecil")　// immutable
list.head // "Alice"
list.tail // List("Bob", "Cecil")

val list2 = "Dan" :: list // List("Dan", "Alice", "Bob", "Cecil")
val nil = List() // 要素０のリスト
```
Scalaのリストのデータ構造
  - 長さ０のリスト List()
  - 先頭の要素(head) と残りの要素のリスト(tail)で構成されるリスト

## パターンマッチ構文
```scala
val list = List(1,2,3,4)
list match {
    case Nil => "empty list"
    case h :: t => s"head: $h, tail: $t"
}

List(100) match {
  case List(x) => x
}

List(1,2,3,4,5) match {
  case x :: y :: t => y // 2
  case _ => -1
}
```

## first-class object(第一級オブジェクト）として扱える関数
- 関数の戻り値となる
- 関数の引数となる
- 変数に格納できる
- 無名のリテラルとして表現可能
```scala
val add = (i: Int) => i + 1

def addN(n: Int) = {
    (i: Int) => i + n
}

List(1,2,3,4).filter(_ % 2 == 0) // List(2,4)
```

## コレクションクラスの基本メソッド
### map （写像関数）
コレクションの各要素に関数を適用する
```scala
// Scala
val list = List(1,2,3,4,5);
list.map(i => i * 2) // List(2,4,6,7,10)

list.map(_ * 2) //　プレースホルダーにより簡略に記述
```
```java
// Java
List<Integer> list = ..... // 省略
List<Integer> result = new ArrayList<Integer>();
for (int i : list) {
    result.add(i * 2)
}
```
### filter
条件に合致する要素を抽出
```scala
val list = List(1,2,3,4)
list.filter(_ > 2)
```
```java
List<Integer> list = ..... // 省略
List<Integer> result = new ArrayList<Integer>();
for (int i : list) {
    if (i > 2) {
      result.add(i * 2);
    }
}
```

### fold関数
畳み込み関数
```scala
val list = List(1,2,3,4,5)
list.foldLeft(0)(_ + _) // 10
list.foldLeft(0)((acc, i) => acc + i) // 冗長に書いた場合（型は省略）
```
```java
List<Integer> list = ..... // 省略
int result = 0;
for (int i : list) {
  result = result + i;
}
```

foldLeft関数をスタンドアロンに実装してみると。。。
```scala
@annotation.tailrec
def foldLeft[A,B](list: List[A])(z: B)(f: (B, A) => B): B = list match {
  case List() => z
  case h :: t => foldLeft(t)(f(z, h))(f)
}

def foldRight[A,B](list: List[A](z: B)(f: (A, B) => B): B = list match {
  case List() => z
  case h ::t => f(h, foldRight(t)(z)(f))
}
```
foldLeftのような構造の再帰関数を特に末尾再帰関数(tail-recursive)と呼ぶ。
末尾再帰関数はコンパイル後は　while文に展開されるためスタックを消費しない。

# case クラス
```scala
case class Person(name: String, age: Int) // これだけ！

val tom = Person("Tom", 10) // immutable
// getter
tom.name // Tom
tom.age // 10
// オブジェクトの状態の変更は不可
// tom.name = "Bob" // error!
// パターンマッチによる値の取り出し
tom match {
  case Person(name, age) => s"$name is $age years old" // Tom is 10 years old!
}

// if も利用可能
tom match {
  case Person(_, age) if age < 20 => "Young"
  case _ => "Adult"
}
```

```java
// Java
class Person {
  private String name;
  private int age;
  
  // getter, setter は省略
}

Person tom = new Person();
tom.setName("Tome");
tom.setAge(10);

// 状態の変更が可能なミュータブルなオブジェクト
```

## Map
```scala
// Scala
val map = Map("Sun" -> 0, "Mon" -> 1, "Tue" -> 2, "Wed" -> 3, "Thur" -> 4, "Fri" -> 5, "Sat" -> 6)

map("Mon") // 1
map("Non") // java.util.NoSuchElementException: key not found: Non

// 型安全な値の取り出し
map.get("Mon") // Some(1)
map.get("Non") // None
map.getOrElse("Mon", -1) // 1
map.getOrElse("Non", -1) // -1 
```
Option型で戻るのでnullチェックが型レベルで強制される。

## Option
戻り値がnullになる可能性があることを型レベルで明示できる。
```scala
// Optionの抽象クラス。持つかもしれない値の型をパラメータでもつ
sealed abstract class Option[+A] extends Product

// Someは、Optionを継承して値がある場合を表現する。
// コンストラクタに実際の値をとる
final case class Some[+A](x: A) extends Option[A]

// Noneは、値がない場合を表す。Optionの型パラメータは共変なので
// Option[Nothing]は全てのOption[A]型のサブタイプになる。なのでシングルトン。
case object None extends Option[Nothing]
```
seald指定されたクラスのついては、パターンマッチに網羅性チェックがかかる。

```scala
def get[A](o: Option[A]) = o match {
  case Some(a) => a
  case None => "No value"
}
```

## for comprehension
map, filter, flatMapのシンタックスシュガー
```scala
val list1 = List(1,2,3,4,5)
val list2 = List("a", "b", "c", "d", "e")

list1.filter(i => i % 2 == 0).flatMap(i => list2.map(j => (i, j)))

for {
  i <- list1 if i % 2 == 0
  j <- list2
} yield (i, j)
// List[(Int, String)] = List((2,a), (2,b), (2,c), (2,d), (2,e), (4,a), (4,b), (4,c), (4,d), (4,e))
```

## Stream
