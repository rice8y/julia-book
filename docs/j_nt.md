# 公称型システムとサブタイピング

Julia の型は, 抽象型と具象型に大別できる.

## 公称型システム

Julia は公称型付けシステムを採用している.

```Julia
julia> typeof(1.0)
Float64

julia> typeof("concrete-type")
String

julia> typeof(1) === Int
true

```

上記は, 具象型の確認例である. ここで, 最後に行に注目すると, `1` には `Int` という名前(型)がついており, その名前からオブジェクトが唯一に定まっていることが分かる. これが公称型付けである.

## 基本型と派生型

Julia には, 数値型を意味する `Number` という型が存在する. ここで, `Number` は `Int` や `Float64` の基本型, `Int` や `Float64` は `Number` の派生型という. また, 2つ以上の型間に「基本型-派生型」の関係が形成されることをサブタイピングという. これらの関係は, 派生型演算子 `<:`, 基本型演算子 `>:` で確認できる.

```Julia
julia> Int <: Number
true

julia> Number >: Int
true

```

派生型演算子では, `(派生型) <: (基本型)` の場合に, 基本型演算子では, `(基本型) >: (派生型)` の場合に, `true` が返ってくる. また, `isa()` でも同様の確認ができる.

```Julia
ulia> isa(1, Int)
true

julia> isa(1, Number)
true

```

上記の例では, `1` が `Int` であり `Number` でもあることを示している. また, `isa()` は演算子 `isa` としても使用できる.

```Julia
julia> 1 isa Int
true

```

## 型階層

```text
Number  (Abstract Type)
├─ Complex
└─ Real  (Abstract Type)
   ├─ AbstractFloat  (Abstract Type)
   │  ├─ Float16
   │  ├─ Float32
   │  ├─ Float64
   │  └─ BigFloat
   ├─ Integer  (Abstract Type)
   │  ├─ Bool
   │  ├─ Signed  (Abstract Type)
   │  │  ├─ Int8
   │  │  ├─ Int16
   │  │  ├─ Int32
   │  │  ├─ Int64
   │  │  ├─ Int128
   │  │  └─ BigInt
   │  └─ Unsigned  (Abstract Type)
   │     ├─ UInt8
   │     ├─ UInt16
   │     ├─ UInt32
   │     ├─ UInt64
   │     └─ UInt128
   ├─ Rational
   └─ AbstractIrrational  (Abstract Type)
      └─ Irrational
```

## 抽象型と具象型

**抽象型**：オブジェクト(インスタンス)を生成できない型  
**具象型**：オブジェクト(インスタンス)を生成できる型

抽象型であるかは `isabstracttype()` で, 具象型であるかは `isconcretetype()` で確認できる.

```Julia
julia> isabstracttype(Int)
false

julia> isconcretetype(Int)
true

julia> isabstracttype(Number)
true

julia> isconcretetype(Number)
false

```

上記の例より, `Int` は具象型であるが, その基本型である `Number` は抽象型であることが分かる.

## Any 型

引数に渡した型の共通の基本型を返す関数として `typejoin()` が存在する.

```Julia
julia> typejoin(Int, Float64)
Real

julia> typejoin(Int, String)
Any

```

上記の例より, `Int` と `Float64` の共通の基本型(木構造的には親)は `Real` であることが分かる. また, `Int` と `String` の共通の基本型として `Any` が返ってきている. これは, Juliaでは互換性のない型同士であっても共通の基本型 `Any` が存在するということである.

```Julia
julia> isabstracttype(Any)
true

julia> length(subtypes(Any))
588

julia> supertype(Any)
Any

```

上記の例より, `Any` は抽象型であることが分かる. また, `Any` の直接の派生型は588個あり, `Any` の基本型は `Any` 自身であることが分かる. つまり, `Any` は全ての型の基本型なのである.

### 参考文献

- 後藤俊介, "実践Julia入門", 技術評論社, 2023. ISBN: 978-4-297-13350-4.
