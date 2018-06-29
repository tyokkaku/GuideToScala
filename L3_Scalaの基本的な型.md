# 3.Scalaの基本的な型

## 3.1 変数と定数

・Scalaではセミコロンをあまり使用することはない

## 3.2 基本データ型

・Scalaにはプロミティブ型がなく、すべてオブジェクト型である。

したがって、基本データ型の変数や数値に対してもメソッドが呼べる

```scala
var num:Int = 10
// num: Int = 10

num.toString
// Scala:String = 10

10.toString
// Scala:String = 10
```

・基本データ型のデフォルト値は、varで変数を宣言したときに_を指定した場合の値のこと

```scala
// デフォルト値
var longNum:Long =_
// longNum:Long = 0
```

## 3.3 リテラル

整数リテラルは、10進数、8進数、16進数で表現できる。

浮動小数点リテラル

- 0の後に.をつけて、その後0個以上の数値が続いたもの
  - Floatの場合は、fをつける
  - デフォルトは、Double
  - 指数は、eをつける

文字リテラル

- シングルクオート
- \u ：Unicode文字
- \コードポイント　： 8進数あるいは16進数で表現した値

文字列リテラル

- ダブルクオート
- 生成される文字列はJavaのString型
- """で囲むとraw stringを使える。raw stringは、エスケープシーケンスを無視する。

シンボルリテラル

- 名前の前にシングルクォートをつける
- マップのキーとして文字列の代わりにSymbolを使うことがある
  - 同じ名前を持った2つのシンボルは、同じオブジェクトを示す

```scala
val s = 'sSymbol
// Symbol = 'sSymbol
s.name

// String = sSymbol
```

## 3.4 配列型

Scalaで配列を仕様するには、Arrayクラスを用いる

```scala
// 型とサイズが決まっている場合
val a1 = new Array[Int](10)

// 型推論
val a2 = Array(1,2,3)
// a2:Array[Int] = Array(1,2,3)
```

配列の取得
  - 配列の取得には、()に添字を指定する
  - 実際は、Arrayメソッドの糖衣構文

```scala
// 値の取得
a2(1)
a2.apply(1)

//値の設定
a2(1) = 10
a2.update(1,10)
```

## 3.5 演算子

Scalaで仕様できる演算子は、すべてメソッドである。

```scala
1 + 2
// Int = 3

// 1オブジェクトのメソッド「+」の引数に2を渡して呼び出している
1 .+(2)
// Int = 3
```

Scalaには、インクリメントとデクリメントが存在しないため、「+=」「-=」を使用する

比較演算子

参照型の比較
  - Java  : equals()メソッドで、各オブジェクトの参照が同じインスタンスを指しているかを比較する
  - Scala : IntでもStringでも「==」「!=」で比較する

### コメント

- 複数業コメントのネストが可能

## 3.5 コレクション

- mutable：可変
- immutable：不変。要素の追加、削除時には、新しいコレクションを作成して返す。基本的にimmutableを使用する。

### シーケンス型

- List：順番に並んでいない。先頭から順番に処理していくのに適する。immutable

```scala
val1 = List("Scala")
// list1:List[java.lang.String] = List(Scala)

list1(0)
// java.lang,String = Scala
```

リストの構築(immutable)

- ::(cons) ：要素とリストの結合
- Nil：空のリストを生成

```scala
val list1 = Nil
// list1:scala.collection.immutable.Nil.type = List()

val list2 = "Scala" :: list1
// list2: List[java.lang.String] = List(Scala)

val list3 = "Java" :: list2
// list3: List[java.lang.String] = List(Scala,Java)

```

### マップ型

- Java:HashMap
- Scala:Map

```scala
// マップの生成
val m = Map[String,Int]("Scala"->1,"Java"->2,"Ruby"->3)

// 値の取り出し
m("Scala")
  // Int = 1

// 存在しないキーを指定すると例外
m("Clojure")
  // NoSuchElementException
```

### タプル型:Tuple型

- Scala特有
- 異なる型の要素を格納できる
- 要素数は22個まで指定できる。

```scala
val t1 = ("Scala",10)
// t1:(Java.lang.String,Int) = (Scala,10)

val t2:Tuple2[String,Int] = ("Java",20)
// t2:(String,Int) = (Java,20)

// 要素の取得
val t1._1
// java.lang.String = Scala

val t1._2
// Int = 10
```

### 3.7 遅延評価

- 変数の前にlazyキーワードを付与すると、遅延評価の対象になる
- 遅延評価変数は、最初に参照されるまで変数を初期化しない
  - 一度参照されたら、その後再度計算されることがない。そのため、アプリケーションの起動を高速にできる

```scala
val x = 1
// x:Int = 1

lazy val lazyX = { println("init val"); x + 1 }
// lazyX = <lazy>

lazyX
// init val
// Int = 2

lazyX  // 2度目からは再計算されない。ただ初期化された値を返すだけ。
// Int = 2
```

### この章で学んだこと

```
基本
・Scalaはすべてオブジェクト型
・配列型、Arrayクラス

コレクション
・コレクションのシーケンス型
・List：immutable、順不同
・Map：キーと値が対応
・Tuple：異なる型の要素を格納できる

遅延評価
・遅延評価。遅延評価変数は、最初に参照されるまで変数を初期化しない。（一回参照されたら計算されなくなるため、アプリケーションの起動が高速になる）
```