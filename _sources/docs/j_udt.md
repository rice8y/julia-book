# ユーザ定義型

ユーザによる定義型は以下のようにする.

```Julia
julia> struct Decimal <: Real
        value::BigInt
        point::Int
       end

```

これは, `Decimal` という複合型を定義している. 具体的には, `Decimal` は `Real` の派生型であり, フィールドとして, BigInt 型の `value` と Int 型の `point` を持っている.

```Julia
julia> isconcretetype(Decimal)
true

```

なお, `Decimal`は, 具象型であり, インスタンスを生成できる.

ユーザが定義できる型には, 抽象型, 構造体, 可変構造体, プリミティブ型が存在する.

## ユーザによる型定義

### 抽象型

```Julia
julia> abstract type MyNumber end

julia> abstract type MyReal <: MyNumber end

julia> abstract type AbstractData{T} end

julia> abstract type AbstractPoint{T <: Real} end

```

抽象型は, `abstract type (型名) end` または `abstract type (型名) <: (基本型) end` のように定義する. なお, 基本型の指定を省略した場合は, 基本型として `Any` が指定される. また, 3, 4番目はパラメトリック抽象型の定義例であり, `{}` の中に型パラメータを指定する. なお, 4番目は型制約が付いており, `where` ではなく `{}` の中で制約を記述する.

### 構造体

```Julia
julia> struct Decimal <: Real
        value::BigInt
        point::Int
       end

julia> struct Point2D{T} <: AbstractPoint{T}
        x::T
        y::T
       end

julia> struct MySingleton end

```

`struct (型名) end` または `struct (型名) <: (基本型) end` のように定義する. 2番目は, `AbstractPoint{T}` を基本型に指定したものである. つまり, パラメトリック抽象型を基本型とした公称的派生型である. また, 2番目では, 型パラメータとして宣言している `T` をフィールドの型アノテーションにも利用している. 3番目のように, フィールドを持たない構造体をシングルトン型という.

### 可変構造体

```Julia
julia> mutable struct Future
        value
        done::Bool
       end

```

`mutable struct (型名) end` または `mutable struct (型名) <: (基本型) end` のように定義する. また, 上記の `value` のようにフィールド定義時に型アノテーションを付けなかった場合, `::Any` が指定される.

ここで, 構造体と可変構造体の違いを比較するために以下の構造体を定義する.

```Julia
julia> struct Past
        value
        done::Bool
       end

julia> mutable struct MPoint2D{T} <: AbstractPoint{T}
        x::T
        y::T
       end

```

構造体と可変構造体の違いを以下に示す.

```Julia
julia> f = Future(10, false)
Future(10, false)

julia> f.value = 20
20

julia> f.done = true
true

julia> f
Future(20, true)

julia> p = Past(10, false)
Past(10, false)

julia> p.value = 20
ERROR: setfield!: immutable struct of type Past cannot be changed
Stacktrace:
 [1] setproperty!(x::Past, f::Symbol, v::Int64)
   @ Base .\Base.jl:38
 [2] top-level scope
   @ REPL[54]:1

```

上記の例より, 可変構造体のフィールドは変更可能であるが, 構造体のフィールドは変更不可能であることが分かる.

### プリミティブ型

```Julia
julia> primitive type Int24 <: Signed 24 end

julia> primitive type Address{T} 64 end

```

`primitive type (型名) (ビット数) end` のように定義する.

上記の例では, `Signed` を基本型とする `Int24` (24ビット符号付き整数)を定義している. なお, 上記の例はプリミティブ型を定義しただけで, 中身はないためこのままでは使えない. 実際に使えるようにするための方法は, [ユーザ定義モジュール](j_module.md)で示す.

## コンストラクタ定義

```Julia
julia> struct Point2D{T} <: AbstractPoint{T}
        x::T
        y::T
       end
    
julia> pt = Point2D(1, 2)
Point2D{Int64}(1, 2)

julia> pt.x
1

julia> pt.y
2

```

上記の例では, `Point2D` という型を定義し, そのインスタンスを生成している. ここで, `Point2D{Int64}(1, 2)` 型のインスタンスを生成した `Point2D()` をコンストラクタという. また, 上記の例のようにフィールドの定義数と引数の数が等しく, それぞれ対応するフィールド値として設定されたインスタンスを生成するコンストラクタをデフォルトコンストラクタという.

以下に, 3種類のコンストラクタを定義する.

```Julia
function MPoint2D(v)
    MPoint2D(v, v)
end

function MPoint2D()
    MPoint2D(0, 0)
end

function MPoint2D{T}() where T <: Real
    MPoint2D(zero(T))
end

```

1番目は引数を1つだけ取るもの, 2番目は引数を取らないもの, 3番目は引数は取らないが型パラメータを指定するものである. なお, これらのコンストラクタは外部コンストラクタという.

上記のコンストラクタの使用例を以下に示す.

```Julia
julia> MPoint2D(1, 2)
MPoint2D{Int64}(1, 2)

julia> MPoint2D(1)
MPoint2D{Int64}(1, 1)

julia> MPoint2D()
MPoint2D{Float64}(0.0, 0.0)

julia> MPoint2D{Int}()
MPoint2D{Int64}(0, 0)

```

1番目はデフォルトコンストラクタ, 2～3番目は上記のコンストラクタである.

これらに対し, 内部コンストラクタの例を以下に示す.

```Julia
mutable struct Future
    value
    done::Bool

    function Future()
        obj = new()
        obj.done = false
        obj
    end
    function Future(value)
        new(value, true)
    end
 end
```

1番目は引数を取らないもの, 2番目は引数を1つ取るものである. また, これらのコンストラクタを内部コンストラクタという.

上記のコンストラクタの使用例を以下に示す.

```Julia
julia> f1 = Future()
Future(#undef, false)

julia> f2 = Future("Concrete Value")
Future("Concrete Value", true)


julia> fDC = Future("Concrete Value", true)
ERROR: MethodError: no method matching Future(::String, ::Bool)

Closest candidates are:
  Future(::Any)
   @ Main REPL[1]:10

Stacktrace:
 [1] top-level scope
   @ REPL[5]:1

```

1番目は引数を取らないもの, 2番目は引数を1つ取るもの, 3番目はデフォルトコンストラクタである. ここで, 内部コンストラクタを定義したことによりデフォルトコンストラクタを利用してインスタンスを生成できなくなったことが分かる.これは, デフォルトインストラクタが暗黙的に定義された内部コンストラクタであるからである. ゆえに, 別の内部コンストラクタを明示的に定義するとデフォルトコンストラクタは利用できなくなる.

**内部コンストラクタ**：インスタンスの生成に制限を設ける  
**外部コンストラクタ**：インスタンス生成にバリエーションを持たせる

### 参考文献

- 後藤俊介, "実践Julia入門", 技術評論社, 2023. ISBN: 978-4-297-13350-4.
