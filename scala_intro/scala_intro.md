Scala
=====


* オブジェクト指向
* 関数型
* 再利用
* 並列化
* 一般化
* テスト容易性

シンタックスから見るScala vs Java
------
* 変数定義

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

* メソッド定義
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

