# 6. Scalaのクラスとオブジェクト

- Scalaは、Javaなどの言語と同様に、クラスを使用できる
- Scalaは、シングルトンオブジェクトを簡単に定義できる
- クラスを対象にしたパターンマッチができる

## 6.1 クラスの基礎

```scala
// クラスの定義
class Programmer {
  var language = "Scala"
  def coding() = println(language + "を使ってコーディングします")
}

// インスタンス化
val pg = new Programmer
// pg:Programmer = Programmer@7184d01

pg.coding()
// Scalaを使ってコーディングします

pg.language = "Java"
// pg.language:java.lang.String = Java

pg.language
// java.lang.String = Java

pg.coding()
// Javaを使ってコーディングします
```

## 6.2 基本コンストラクタと補助コンストラクタ

コンストラクタ

- コンストラクタ引数にvalやvarを指定しないで引数を記述した場合、そのフィールドへクラス外からアクセスできない
- アクセス修飾子がなければpublic。自クラスのみに公開する場合は「private」と付ける

```scala
class Programmer(var language:String){
  println("Programmerインスタンスを生成します")
  println("language = " + language)

  def coding() = println(language + "を使ってコーディングします")
}
defined class Programmer

val pg = new Programmer("Java")
// Programmerインスタンスを生成します
// language = Java
// pg:Programmer = Programmer@4b31e246

pg.coding()
// Javaを使ってコーディングします

// 宣言した引数をクラス外に公開する2つの方法
class Programmer(val language:String){ // valかvarで宣言
class Programmer(_language:String){ // _をつける
  println("language = " + _language)
```

補助コンストラクタ

- クラスに複数のコンストラクタを定義できる
- ``def this({任意の引数}) = {補助コンストラクタの処理}``

```scala
class Programmer(val language:String) {
  println("Programmerインスタンスを生成します")
  println("language = " + language)

  /** 補助コンストラクタ */
  def this() = this("Scala")

  def coding() = println(language + "を使ってコーディングします")
}
```

## 6.3 事前条件を指定するrequire

- 引数の検証
- クラスや関数の利用者に対して制約を課す(コンストラクタやメソッド内の)

```scala
class Programmer(_language: String) {
  println("Programmerインスタンスを生成します")
  println("language = " + _language)
  
  // 引数がnullだったらエラー
  require(_language != null)
  val language = _language

  /** 補助コンストラクタ */
  def this() = this("Scala")

  def coding() = println(language + "を使ってコーディングします")
}
```

## 6.4 引数の型や数が異なるメソッドを定義するオーバーロード

```scala
class Sample {
  def add(x:Int, y:Int) = x + y
  def add(x:Double, y:Double) = x + y
  def add(x:Int, y:Int, z:Int) = x + y + z
}
// defined class Sample

val s = new Sample
s.add(1,2)
// Int = 3
s.add(1D,2D)
// Double = 3.0
s.add(1,2,3)
// Int = 6
```

## 6.5 シングルトンオブジェクトの作成

ユーティリティクラス(private)の作成方法
  - Java ： privateでインスタンス化を禁止したstaticメソッドを定義する
  - Scala : シングルトンオブジェクトを使う。(プログラム内にインスタンスが1つしかないクラスのオブジェクト)
    - Scalaにはstaticキーワードが存在しない

シングルトンオブジェクトの作成

- objectキーワードを使う
- 初回アクセス時にインスタンスが自動的に生成される
- 引数を指定できない
- 補助コンストラクタを持てない

```scala
// objectを使用すればシングルトンオブジェクトを定義できる
object SampleUtil{
  def hello() = println("hello")
}
SampleUtil.hello()
// hello
```

## 6.6 コンパニオンオブジェクトとapply

- 任意のクラスと同等のスコープ、名前で定義されたシングルトンオブジェクトのこと
- コンパニオンクラスのprivateなフィールドやメソッドにアクセスできる

```scala
class SampleCompanion private (num:Int)

object SampleCompanion {
  def apply(num:Int) = {
    new SampleCompanion(num)
  }
}

object Main{
  def main(args:Array[String]) = {
    // Sampleompanion.apply(10)とするところだが、applyメソッドは特別に、以下のように記述し、インスタンスを生成している
    val ins = SampleCompanion(10)
    println(ins)
  }
}
```

- apply：呼び出しを簡易にできる。オブジェクトのメソッドを、関数を扱うように呼び出すことができる。ファクトリ的な使い方をするケースが多い

## 6.7 オブジェクトのパターンマッチ

Option型

- Java: オブジェクトの参照先がない場合は、nullが代入される
- Scala: Option型を使用する。戻り値がない場合は「None」、存在する場合は「Some」を返す
  - Optionクラス、サブクラスにSomeクラスとNoneクラスを持つ
  - 値の取得は「get」メソッドを使う。Some型であれば実際の値、None型であれば例外が発生する
  - 「getOrElse」メソッドを用いれば、None型でも指定した値を返す

```scala
val m = Map("Scala" -> 1, "Java" -> 2)
// scala.collection.immutable.Map[java.langa.String,Int] = Map(Scala -> 1, Java -> 2)

m.get("Scala")
// Option[Int] = Some(1)
m.get("Ruby")
// Option[Int] = None
m.get("Ruby").getOrElse("nothing!")
// Any = nothing!
```

Option型のパターンマッチ

```scala
def testMatch(opt:Option[Int]) = {
  opt match {
    case Some(n) => println(n)
    case None => println("None!")
  }
}
textMatch(Some(10))
// 10
testMatch(None)
// None!
```

unapplyを使用したパターンマッチ

- オブジェクトを、パターンマッチできる
- ただし、自分で定義したクラスは、インスタンス構成要素を抽出しなければ、パターンマッチできない
  - 抽出するには、抽出子「unapply」を使用する

```scala
class Apple
object Apple {
  // 自作クラスのオブジェクトをパターンマッチで利用したい場合は、unapplyを使用する
  def unapply(a:Any):Boolean = {
    if(a.isInstanceOf[Apple])true else false // isInstanceOfで型のチェックが可能
  }
}

class Orange(val name:String)
object Orange {
  def apply(name:String):Orange = new Orange(name)
  def unapply(a: Orange):Option[String] = Some(a.name)
}

def matchTest(value:Any) = {
  value match {
    case Apple => println("Apple")
    case Orange("DEKOPON") => println("Orange.name=DEKOPON")
    // 変数にバインド
    case Orange(name) => println("Orange.name=" + name)
    case _ => println("other")
  }
}

matchTest(Orange("something"))
// other
matchTest(Orange("DEKOPON"))
// Orange,name=DEKOPON
matchTest(Apple)
// Apple
matchTest("hello")
// other
```

## 6.8 caseクラスのパターンマッチ

- caseクラス
  - インスタンス化 ： applyが定義されているため、newを使用しなくてもインスタンス化できる
  - 引数  ： 引数はすべてvalで扱われる
  - メソッド  ： toString,equals,copyなどが自動で実装される
  - パターンマッチ ： unapplyが定義されているため、パターンマッチできる
- 使用用途が想定された便利機能が標準装備されているクラスのこと

```scala
// caseクラスの宣言
case class Apple(name:String)
// defined class Apple
case class Orange(name.String)
// defined class Orange

// caseクラスの便利メソッド
val apple = Apple("fuji")
apple.name
apple.toString
val newApple = apple.copy(name = "jonagold")
// newApple: Apple = Apple(jonagold)

// unapplyを実装しなくても、パターンマッチができる
def matchTest(value:Any) = {
  value match {
    case Apple(name) => println("Apple.name" + name)
    case Orange("DEKOPON") => println("DEKOPON")
    // 変数にバインド
    case Orange(name) => println("Orange.name=" + name)
    case _ => println("other")
  }
}
```

### この章で学んだこと

```
基本
・Scalaは、シングルトンオブジェクトの作成が簡単
・Scalaは、クラスを対象にしたパターンマッチが特に便利

機能
・基本コンストラクタの他に補助コンストラクタを定義できる
・``def this({任意の引数}) = {補助コンストラクタの処理}``
・requireで、引数を検証できる
・引数の数や型が異なっていてもオーバーロードできる
・applyは、呼び出しを容易にする。関数のように呼び出せる。ファクトリ的に使う

シングルトンオブジェクト/コンパニオンオブジェクト
・シングルトンオブジェクトは、ユーティリティクラスのようなもの
・コンパニオブジェクトは、シングルトンオブジェクトの一種
・コンパニオンオブジェクトにすれば、コンパニオンクラスのprivateなフィールドやメソッドにアクセスできる

Option型
・Option型のオブジェクトは、パターンマッチを使って、null-safeを実現できる
・自作のクラスの場合は、unapplyを使って、パターンマッチができる

caseクラス
・caseクラスは便利機能を標準装備している
・パターンマッチもそのまま使える
```