# 特別な型

## Tuple 型・NamedTuple 型

タプル (Tuple) とは, 複数の値の組み合わせを指し, `()` の中に, 値をカンマ区切りで記述する.

```Julia
julia> typeof((1, 2))
Tuple{Int64, Int64}

julia> typeof((1, 99.9, :a))
Tuple{Int64, Float64, Symbol}

julia> typeof((1,))
Tuple{Int64}

julia> typeof(())
Tuple{}

```

Tuple 型は型パラメータ数が可変なパラメトリック型と見做せる. ここで, 要素が1つだけのタプルは `(1,)` のようにカンマも記述することに注意. カンマを記述せずに要素を1つだけにすると, `typeof((1)) === Int64` となり, だた括弧 `()` で括っただけとなる. また, 型パラメータを指定しない `Tuple` と空の `{}` を指定する `Tuple{}` は別物である.

```Julia
julia> Tuple === Tuple{}
false

```

また, 同じ型が並ぶタプルを表す型として, `NTuple{N, T}` が存在する.

```Julia
julia> (1, 2) isa NTuple{2, Int}
true

julia> (1, 2.0) isa NTuple{2, Int}
false

```

`Tuple` と `Tuple{}` についての検証を以下に示す.

```Julia
julia> isabstracttype(Tuple)
false

julia> isconcretetype(Tuple)
false

julia> isconcretetype(Tuple{})
true

julia> isconcretetype(Tuple{Int})
true

julia> typeof(Tuple)
DataType

julia> typeof(Tuple{Int})
DataType

julia> supertype(Tuple)
Any

julia> subtypes(Tuple)
Type[]

julia> Tuple{Int} <: Tuple
true

julia> Tuple{Int, Int} <: Tuple{Int}
false

julia> Tuple{Int} <: Tuple{Integer} <: Tuple{Any}
true

julia> Tuple{Int64, Float64, Symbol} <: NTuple{3, Any}
true

```

上記の例より, `Tuple` は抽象型でもなく具象型でもないが, 型パラメータを指定すると具象型となる. また, 空の型パラメータを指定した `Tuple{}` は具象型となる. さらに, Tuple 型の公称的基本型は `Any` , 公称的派生型は存在せず, 型パラメータを指定した Tuple 型は, 型パラメータを指定していない Tuple 型の派生型となる.

`Tuple{Int, Int} <: Tuple{Int}` が `false` より, 2つの `Int` を要素として持つタプルは, 1つの `Int` を要素として持つタプルの派生型ではないことが分かる. 具体的には, `(1, 2)` と `(1,)` の間にサブタイピング関係はないということである. また, `Tuple{Int} <: Tuple{Integer} <: Tuple{Any}` が `true` である. パラメトリック型では, 型パラメータが異なれば, サブタイピングの関係にあるかどうかに関わらず型として互換性のない別物になる (不変). 例としては, `Array{Int} <: Array{Integer}` が `false` . しかし, `Tuple` ではこれが当てはまらず, `A <: B` が `true` / `false` なら `Tuple{A} <: Tuple{B}` は `true` / `false` となる. このように, 型パラメータの基本型-派生型の関係がそのまま型の基本型-派生型の関係に反映される性質のことを共変という.

すなわち, Tuple 型は要素数を重視する型であるといえる.

```Julia
julia> Tuple{Int64, Symbol, Bool} <: NTuple{3, Any}
true

```

上記の例より, 各要素が全く互換性のない3つの型からなるタプルは, `NTuple{3, Any}` の派生型, つまり要素の型に制約はないが要素数は3であるタプルということである.

```Julia
julia> nt = (a=1, b=2.1, c=:OK)
(a = 1, b = 2.1, c = :OK)

julia> typeof(nt)
NamedTuple{(:a, :b, :c), Tuple{Int64, Float64, Symbol}}

julia> nt1 = (a=1,)
(a = 1,)

julia> nt2 = (;a=1)
(a = 1,)

julia> typeof(nt1)
NamedTuple{(:a,), Tuple{Int64}}

julia> nt0 = (;)
NamedTuple()

julia> typeof(nt0)
NamedTuple{(), Tuple{}}

```

上記の例のように, `(a=1, b=2.1, c=:OK)` として各要素に名前を付けたタプルを名前付きタプルといい, その型を NamedTuple 型という.

```Julia
julia> (1, 2.1, :OK) isa Tuple{Int64, Float64, Symbol}
true

```

また, 上記の例より分かる通り, NamedTuple 型は, Tuple 型をラップして各要素に名前でアクセスできるようにした型である. さらに, 名前付きタプルは以下のように `@NamedTuple` マクロを用いて簡単に記述することができる.

```Julia
julia> @NamedTuple begin
        a::Int
        b::Float64
        c::Symbol
       end
NamedTuple{(:a, :b, :c), Tuple{Int64, Float64, Symbol}}

julia> @NamedTuple{a::Int, b::Float64, c::Symbol}
NamedTuple{(:a, :b, :c), Tuple{Int64, Float64, Symbol}}

julia> @NamedTuple{a, b}
NamedTuple{(:a, :b), Tuple{Any, Any}}

julia> @NamedTuple{}
NamedTuple{(), Tuple{}}

```

`NamedTuple` についての検証を以下に示す.

```Julia
julia> supertype(NamedTuple)
Any

julia> subtypes(NamedTuple)
Type[]

julia> isabstracttype(NamedTuple)
false

julia> isconcretetype(NamedTuple)
false

julia> isconcretetype(NamedTuple{})
false

julia> isconcretetype(NamedTuple{Int})
false

julia> typeof(NamedTuple)
UnionAll

julia> typeof(NamedTuple{})
UnionAll

julia> typeof(@NamedTuple{})
DataType

julia> @NamedTuple{a::Int, b::Float64, c::Symbol} <: @NamedTuple{a, b, c}
false

```

`NamedTuple` は抽象型でもなく具象型でもない. また, `NamedTuple` の公称的基本型は `Any` であり, 公称的派生型は存在しない. なお, `Tuple` に見られた共変は `NamedTuple` にはない.

## Union 型

型パラメータに指定した型の和集合を表す型を Union 型という.

```Julia
julia> Union{Int, String}
Union{Int64, String}

julia> Union{Int, Float64, String}
Union{Float64, Int64, String}

julia> Union{Int, Integer}
Integer

julia> Union{Int, Int} === Union{Int} === Int
true

```

上記の例より, サブタイピング関係にない複数の型が指定された場合, 単純にそれらの和集合となり, サブタイピング関係がある複数の型が指定された場合, `Any` に近い方の基本型だけが残る. 例として, `A <: B` ならば `Union{A, B} === B` となる.

```Julia
julia> isabstracttype(Union{Int, String})
false

julia> isconcretetype(Union{Int, String})
false

julia> supertype(Union{Int, String})
ERROR: MethodError: no method matching supertype(::Type{Union{Int64, String}})

Closest candidates are:
  supertype(::DataType)
   @ Base operators.jl:43
  supertype(::UnionAll)
   @ Base operators.jl:44

Stacktrace:
 [1] top-level scope
   @ REPL[63]:1

julia> subtypes(Union{Int, String})
Type[]

julia> Union{Int, String} <: Any
true

julia> Int <: Union{Int, String}
true

julia> String <: Union{Int, String}
true

julia> Float64 <: Union{Int, String}
false

julia> typeof(Union{Int, String})
Union

```

上記の例より, `Union` は抽象型でもなく具象型でもない. また, 公称的派生型は存在しない. なお, `supertype(Union{Int, String})` でエラーが発生したのは, `supertype()` が公称的基本型を返す関数であるためである. 具体的には, `supertype()` は型定義時に `struct X <: Y ～ end` 等のように定義した場合に, `supertype(X) === Y` となるものを返すが, そのように定義したものは `DataType` か `UnionAll` のいずれかしか存在しないため, エラーが発生したのである.

## ボトム型

```Julia
julia> Union{}
Union{}

julia> typeof(Union{})
Core.TypeofBottom

julia> Union{} <: Int
true

julia> all(Union{} <: T for T in (Float64, String, Number, Any))
true

```

上記の例より, `Union{}` は `Core.TypeofBottom` のインスタンスであることが分かる. また, `Union{}` は全ての型の派生型である. ゆえに, ボトム型という. (対照的に, 全ての型の基本型である `Any` はトップ型ともいう. )

## シングルトン型

ある型 `T` に対して `a isa T` かつ `b isa T` であるような `a`, `b` がある際に必ず `a == b` つまり `a` と `b` がオブジェクトレベルで同一レベルとなるなら, `a(b)` は型 `T` のシングルトンであるという. また, この時の型 `T` をシングルトン型という.

```Julia
julia> struct MySingleton end

julia> a = MySingleton()
MySingleton()

julia> b = MySingleton()
MySingleton()

julia> Base.issingletontype(MySingleton)
true
```

上記の例より, フィールドのない構造体を定義すると, それはシングルトン型となり, そのインスタンスは一意に定まっていることが分かる. また, シングルトン型かどうかを確認する関数として `Base.issingleton()` が存在する.

パラメトリック型のシングルトン型は以下のようになる.

```Julia
julia> Base.issingletontype(ParametricSingleton{})
false

julia> Base.issingletontype(ParametricSingleton{Int})
true

julia> all((ParametricSingleton{T}() === ParametricSingleton{T}() for T in (Int, Float64, String)))
true

```

上記の例より, 型パラメータが指定されていない場合はシングルトン型ではないが, 型パラメータが指定されている場合はシングルトン型であることが分かる. また, 型パラメータを指定したもののインスタンスは一意に定まる.

Julia には標準で定義されているシングルトン型も存在する.

```Julia
julia> Base.issingletontype(Nothing)
true

julia> Nothing() === nothing
true

julia> Base.issingletontype(Missing)
true

julia> Missing() === missing
true
```

## Type{T} 型セレクタ

ある型 `T` に対して `typeof(T)` とすると, `DataType`, `UnionAll`, `Union` のいずれかが返ってくる. これらは, `Type` の派生型である.

```Julia
julia> subtypes(Type)
4-element Vector{Any}:
 Core.TypeofBottom
 DataType
 Union
 UnionAll

```

```Julia
julia> typeof(Type)
UnionAll

julia> typeof(Type{Int})
DataType

julia> Int isa Type{Int}
true

julia> Float64 isa Type{Int}
false

julia> all(T isa Type{T} for T in (Float64, String, Any))
true

julia> all(T isa Type{<:Real} for T in (Int, Float64, Signed, AbstractFloat))
true

```

`Type` はパラメトリック型であり, 型パラメータを指定していない場合 `UnionAll`, 型パラメータを指定している場合 `DataType` となる. また, 型 `T` を型パラメータに指定すると, `T isa Type{T}` は `true` になるが, 型パラメータとして指定した型と `T` の型が異なれば, `T isa Type{T}` は `false` となる. ただし, 型制約を指定した場合, 例として `A <: B` ならば `A isa Type{<:B}` は `true` となる. このような `Type{T}` を型 `T` の型セレクタという.

## 型エイリアス

型には別名を付けることができ, これを, 型エイリアスという. 型エイリアスの例を以下に示す.

```Julia
julia> typeof(1.0 + 1.0im)
ComplexF64 (alias for Complex{Float64})

julia> typeof([1. 2.; 3. 4.])
Matrix{Float64} (alias for Array{Float64, 2})

julia> Vector
Vector (alias for Array{T, 1} where T)

```

なお, 型エイリアスはユーザ定義可能である.

```Julia
julia> struct ParametricSingleton{T} end

julia> const IntParametricSingleton = ParametricSingleton{Int}
IntParametricSingleton (alias for ParametricSingleton{Int64})

julia> const IntegerParametricSingleton{T<:Integer} = ParametricSingleton{T}
IntegerParametricSingleton (alias for ParametricSingleton{T} where T<:Integer)

```

上記の例のように, `const` を用いて定数として定義する.

### 参考文献

- 後藤俊介, "実践Julia入門", 技術評論社, 2023. ISBN: 978-4-297-13350-4.
  