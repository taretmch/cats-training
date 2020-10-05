# 1. Introduction

- この章では，Cats のコードベースをみていく．
- 型クラスの例として，`Show` と `Eq` を取り上げる．
- 最後に，型クラスを代数的データ型，パターンマッチング，値クラス，型エイリアスと結びつける．これらは，Scala における関数型プログラミングに対する構造的なアプローチである．

## 1.1 Anatomy of a Type Class (型クラスについて)

- 型クラスのパターンには，3つの重要な要素がある:
    - 型クラス自身
    - 特定の型のインスタンス
    - 型クラスを使うメソッド
- Scala において，型クラスは，implicit な値とパラメータを使って，任意で implicit なクラスを用いて実装される．
- 型クラスの要素に対応するもの:
    - トレイト: 型クラス
    - implicit な値: 型クラスのインスタンス
    - implicit なパラメータ: 型クラスの利用
    - implicit なクラス: 型クラスを使いやすくするための任意のユーティリティ

### 1.1.1 The Type Class (型クラス)

- 型クラスは，実装要件を表現するインターフェースや API である．
- Scala において，型クラスは1つ以上の型パラメータを持ったトレイトによって表される．
- 例)

```scala
// Define a very simple JSON AST
sealed trait Json
final case class JsObject(get: Map[String, Json]) extends Json
final case class JsString(get: String) extends Json
final case class JsNumber(get: Double) extends Json
final case object JsNull extends Json

// The "serialize to JSON" behaviour is encoded in this trait
trait JsonWriter[A] {
  def write(value: A): Json
}
```

### 1.1.2 Type Class Instances

- 型クラスのインスタンスは，我々が気にする特定の型のための型クラスの実装を提供する．
- Scala において，型クラスの実装を作成して `implicit` キーワードでタグ付することによってインスタンスを定義できる．

```scala
final case class Person(name: String, email: String)

object JsonWriterInstances {
  implicit val stringWriter: JsonWriter[String] =
    new JsonWriter[String] {
      def write(value: String): Json =
        JsString(value)
    }

  implicit val personWriter: JsonWriter[Person] =
    new JsonWriter[Person] {
      def write(value: Person): Json =
        JsObject(Map(
          "name" -> JsString(value.name),
          "email" -> JsString(value.email)
        ))
    }

  // etc...
}
```
