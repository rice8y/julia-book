# ユーザ定義モジュール

Julia でモジュールを定義し, 実際に使用するまでの過程を以下に示す.

まず, モジュールを作成する. ここでは, [ユーザ定義型>プリミティブ型](j_udt.md#プリミティブ型)で例として示した `primitive type Int24 <: Signed 24 end` を実装する.

```Julia
module Int24Module

export Int24

primitive type Int24 <: Signed 24 end

const INT24_MIN = -2^23
const INT24_MAX = 2^23 - 1

Int24(a::Int) = INT24_MIN <= a <= INT24_MAX ? a : throw(InexactError(:Int24, Int24, a))

Base.show(io::IO, x::Int24) = print(io, Int32(x) << 8 >> 8)

Base.:+(a::Int24, b::Int24) = Int24(Int32(a) + Int32(b))
Base.:-(a::Int24, b::Int24) = Int24(Int32(a) - Int32(b))
Base.:*(a::Int24, b::Int24) = Int24(Int32(a) * Int32(b))
Base.:/(a::Int24, b::Int24) = Int24(Int32(a) / Int32(b))

Base.:(==)(a::Int24, b::Int24) = Int32(a) == Int32(b)
Base.:<(a::Int24, b::Int24) = Int32(a) < Int32(b)
Base.:<=(a::Int24, b::Int24) = Int32(a) <= Int32(b)
Base.:>(a::Int24, b::Int24) = Int32(a) > Int32(b)
Base.:>=(a::Int24, b::Int24) = Int32(a) >= Int32(b)

Base.Int(a::Int24) = Int32(a) << 8 >> 8

end
```

上記のモジュールを, `Int24Module.jl` として `C:\users\yoneyama\JuliaMyModule\Int24Module\` に保存する. (ファイル名, ファイルのパスは任意の物に置き換えること. )

この状態で `using` すると以下のようなエラーが発生する.

```Julia
julia> using Int24Module
ERROR: ArgumentError: Package Int24Module not found in current path.
- Run `import Pkg; Pkg.add("Int24Module")` to install the Int24Module package.
Stacktrace:
 [1] macro expansion
   @ .\loading.jl:1595 [inlined]
 [2] macro expansion
   @ .\lock.jl:267 [inlined]
 [3] require(into::Module, mod::Symbol)
   @ Base .\loading.jl:1576

```

このエラーメッセージは, `Int24Module` が見つからないことを示している. Julia で `using` する際には, `LOAD_PATH` にそのモジュールが存在している必要がある (`import` も同様). ここで, `LOAD_PATH` を確認してみる.

```Julia
julia> LOAD_PATH
3-element Vector{String}:
 "@"
 "@v#.#"
 "@stdlib"

```

`LOAD_PATH` に `C:\users\yoneyama\JuliaMyModule\Int24Module\` が存在していないことが分かる. ここで, `push!()` を用いて `LOAD_PATH` に `C:\users\yoneyama\JuliaMyModule\Int24Module\` を追加する.

```Julia
julia> push!(LOAD_PATH, "C:\\users\\yoneyama\\JuliaMyModule\\Int24Module\\")
4-element Vector{String}:
 "@"
 "@v#.#"
 "@stdlib"
 "C:\\users\\yoneyama\\JuliaMyModule\\Int24Module\\"

```

`LOAD_PATH` に `C:\users\yoneyama\JuliaMyModule\Int24Module\` が追加された. もう一度 `using` してみる.

```Julia
julia> using Int24Module
[ Info: Precompiling Int24Module [top-level]

```

上手く `using` できている. 実際に `Int24` を使ってみる.

```Julia
julia> Int24(2^23-1)
8388607

julia> Int24(-2^23)
-8388608

julia> Int24(2^23)
ERROR: InexactError: Int24(8388608)
Stacktrace:
 [1] Int24(a::Int64)
   @ Int24Module C:\users\yoneyama\JuliaMyModule\Int24Module.jl:10
 [2] top-level scope
   @ REPL[10]:1

```

上記の例より, `Int24` の範囲に収まる場合にはそのまま値を返し, `Int24` の範囲を超える場合には `InexactError` が発生していることが分かる. なお, 今回は簡単な四則演算とオーバーフロー時のエラーを実装したのみである.
