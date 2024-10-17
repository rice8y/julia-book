# リテラル

## 数値リテラル

### 整数値

整数リテラルの例を以下に示す.

```Julia
julia> 1
1

julia> -1
-1

julia> 1_000_000
1000000

julia> 0x123456789abcdef
0x0123456789abcdef

julia> 0o1234567
0x00053977

julia> 0b11010101
0xd5
 
```

桁区切りに `_` を用いることができる (なお, 区切る桁数に規定はない). また, プレフィックスを使い分けることで16進表記・8進表記・2進表記のリテラルを扱うことができる.

|プレフィックス|リテラル|要素|
|---|---|---|
|`0x`|16進表記|0-9, a-f, A-F|
|`0o`|8進表記|0-7|
|`0b`|2進表記|0, 1|

以下に整数値リテラルの一覧を示す.

|型|符号付き|ビット数|最小値|最大値|
|---|---|---|---|---|
|`Int8`|$\checkmark$|$8$|$-2^{7}$|$2^{7}-1$|
|`UInt8`||$8$|$0$|$2^{8}-1$|
|`Int16`|$\checkmark$|$16$|$-2^{15}$|$2^{15}-1$|
|`Int16`||$16$|$0$|$2^{16}-1$|
|`Int32`|$\checkmark$|$32$|$-2^{31}$|$2^{31}-1$|
|`UInt32`||$32$|$0$|$2^{32}-1$|
|`Int64`|$\checkmark$|$64$|$-2^{63}$|$2^{63}-1$|
|`UInt64`||$64$|$0$|$2^{64}-1$|
|`Int128`|$\checkmark$|$128$|$-2^{127}$|$2^{127}-1$|
|`UInt128`||$128$|$0$|$2^{128}-1$|
|`Bool`|$\mathrm{N/A}$|$8$|`false` $(0)$|`true` $(1)$|
|`Char`||$32$|`U+e0000`|`U+10ffff`|

なお, `Char` の有効範囲は, `U+e0000` から `U+D7ff` までと, `U+e000` から `U+10ffff` までである.

### 浮動小数点数

浮動小数点数リテラルの例を以下に示す.

```Julia
julia> 1.0
1.0

julia> -2.
-2.0

julia> .1
0.1

julia> 1_000.000_000
1000.0

julia> 3.14e2
314.0

julia> 1e-17
1.0e-17

julia> 1f10
1.0f10

julia> 0x1f.8p0
31.5

```

指数表記 `e` を `f` で記述しても浮動小数点数リテラルとなるが, 前者は `Float64` , 後者は `Float32` となる.

|型|精度|ビット数|
|---|---|---|
|`Float16`|半精度|$16$|
|`Float32`|単精度|$32$|
|`Float64`|倍精度|$64$|

### 有理数 / 複素数

有理数リテラルの例を以下に示す.

```Julia
julia> 1//12
1//12

julia> -12//20
-3//5

julia> 3//0
1//0

julia> 0//0
ERROR: ArgumentError: invalid rational: zero(Int64)//zero(Int64)
Stacktrace:
 [1] __throw_rational_argerror_zero(T::Type)
   @ Base .\rational.jl:32
 [2] Rational
   @ .\rational.jl:34 [inlined]
 [3] Rational
   @ .\rational.jl:39 [inlined]
 [4] //(n::Int64, d::Int64)
   @ Base .\rational.jl:62
 [5] top-level scope
   @ REPL[28]:1

```

有理数除算演算子 `//` の前後に整数値リテラルを記述すると有理数が返ってくる. なお, `1//0` は許容されているが, `0//0` は `invalid rational` となり許容されない.

複素数リテラルの例を以下に示す.

```Julia
julia> im
im

julia> 1 + im
1 + 1im

julia> 1.0 + 1.0im
1.0 + 1.0im

```

虚数単位は `im` という定数となっている. 複素数は, `a + b*im` という形で記述する.

## 文字リテラル / 文字列リテラル

文字リテラルの例を以下に示す.

```Julia
julia> 'a'
'a': ASCII/Unicode U+0061 (category Ll: Letter, lowercase)

julia> 'π'
'π': Unicode U+03C0 (category Ll: Letter, lowercase)

julia> 'あ'
'あ': Unicode U+3042 (category Lo: Letter, other)

julia> '漢'
'漢': Unicode U+6F22 (category Lo: Letter, other)

julia> '😊'
'😊': Unicode U+1F60A (category So: Symbol, other)

julia> ''
ERROR: syntax: invalid empty character literal
Stacktrace:
 [1] top-level scope
   @ none:1

julia> 'ab'
ERROR: syntax: character literal contains multiple characters
Stacktrace:
 [1] top-level scope
   @ none:1

julia> '\n'
'\n': ASCII/Unicode U+000A (category Cc: Other, control)

```

文字リテラルは, シングルクォーテーション `''` で括った一文字であり, 0文字または2文字以上指定すると構文エラーとなる. ただし, エスケープシーケンスは記述可能である.

文字列リテラルの例を以下に示す.

```julia
julia> "a"
"a"

julia> "abc"
"abc"

julia> "ひらがな漢字"
"ひらがな漢字"

julia> "\x41a\u3042"
"Aaあ"

julia> "1行目
        2行目
        3行目"
"1行目\n 2行目\n 3行目"

julia> """
       1行目
       2行目
       3行目"""
"1行目\n2行目\n3行目"

julia> ""
""

```

文字列リテラルは, ダブルクォーテーション `""` で括った文字列である. また, 文字列リテラルでは改行を扱うことができるが, 複数行の文字列は3連ダブルクォーテーション `"""～"""` で括る記法がしばしば用いられる. これらには以下のような違いがある.

|`""～""`|`"""～"""`|
|---|---|
|最初の行が改行される|最初の行が改行されない|
|`"` を文字として入れる際には `\"` とする|`"` を文字として入れる際にはそのまま `"`とする|

```Julia
julia> "
       1
       2"
"\n1\n2"

julia> """
       1
       2"""
"1\n2"

julia> " \" "
" \" "

julia> """ " """
" \" "

```

また, 文字列リテラルでは式展開が行える.

```Julia
julia> x = 1;

julia> "x = $x"
"x = 1"

julia> "Odd Number: $(2x + 1)"
"Odd Number: 3"

```

参照したい評価式を `$(～)` のように `()` に括って記述する. なお, 評価式が1文字の場合, `()` は省略できる.

さらに, 文字列リテラルには, raw 文字列リテラルが存在する. raw 文字列リテラルの例を以下に示す.

```Julia
julia> b = 9
9

julia> raw"a\t$b\n\u3042"
"a\\t\$b\\n\\u3042"

julia> "a\t$b\n\u3042"
"a\t9\nあ"

```

`raw"～"` で括ることで文字列をそのまま出力することができる.

## 正規表現リテラル

正規表現リテラルの例を以下に示す.

```Julia
julia> r"^a[b-f]+$"
r"^a[b-f]+$"

julia> r"^a[b-f]+$"i
r"^a[b-f]+$"i

```

正規表現は `r"～"` で記述する. また, 2番目の例のようにサフィックスを付けることもできる. なお, `typeof(r"^a[b-f]+$")` は `Regex` .

## 非標準文字列リテラル

非標準文字列リテラルの例を以下に示す. なお, 前節で述べた raw 文字列リテラルや正規表現リテラルも非標準文字列リテラルであるがここでは省略する.

|表記|名称|説明|
|---|---|---|
|`b"～"`|バイト列リテラル|文字列ではなく (UTF-8 エンコードの) バイト列 (UInt8 の 1次元配列) を返す|
|`MIME"～"`|MIME タイプリテラル|MIME タイプを表す|
|`html"～"`|HTML リテラル|HTMLオブジェクトを生成|
|`text"～"`|Text リテラル|Text オブジェクトを生成|
|`v"～"`|バージョン番号リテラル|バージョン番号 (セマンティックバージョニング準拠) を表す|
|`var"～"`|変数参照リテラル|特殊な名前の変数を参照する|

```Julia
julia> b"DATA\xff\u2200"
8-element Base.CodeUnits{UInt8, String}:
 0x44
 0x41
 0x54
 0x41
 0xff
 0xe2
 0x88
 0x80

julia> MIME"text/html"
MIME{Symbol("text/html")}

julia> html"<p>Hello, World!</p>"
HTML{String}("<p>Hello, World!</p>")

julia> text"Text Node"
Text Node

julia> v"1.6.0-beta1.0"
v"1.6.0-beta1.0"

julia> x = 1
1

julia> var"x"
1

```

## 範囲リテラル

範囲リテラルの例を以下に示す.

```Julia
julia> 1:10
1:10

julia> 1:10 == [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
true

julia> 1:0.1:2
1.0:0.1:2.0

julia> 1:0.1:2 == [1.0, 1.1, 1.2, 1.3, 1.4, 1.5, 1.6, 1.7, 1.8, 1.9, 2.0]
true

julia> collect(-10:5:10)
5-element Vector{Int64}:
 -10
  -5
   0
   5
  10

julia> abc = 'a':'c'
'a':1:'c'

julia> collect(abc)
3-element Vector{Char}:
 'a': ASCII/Unicode U+0061 (category Ll: Letter, lowercase)
 'b': ASCII/Unicode U+0062 (category Ll: Letter, lowercase)
 'c': ASCII/Unicode U+0063 (category Ll: Letter, lowercase)
```

範囲リテラルは, `(開始値):(ステップ値):(終了値)` のようにして記述する. なお, ステップ値は省略でき, その場合ステップ値は1となる.

## 配列リテラル

ベクトル (1次元配列) の例を以下に示す.

```Julia
julia> [1, 2, 3]
3-element Vector{Int64}:
 1
 2
 3

julia> [1.0, 2.0, 3.0]
3-element Vector{Float64}:
 1.0
 2.0
 3.0

```

ベクトル (1次元配列) は, 大括弧 `[]` の中で値をカンマ `,` で区切って記述する.

行列 (2次元配列) の例を以下に示す.

```Julia
julia> [1 2
       3 4]
2×2 Matrix{Int64}:
 1  2
 3  4

julia> [1 2; 3 4]
2×2 Matrix{Int64}:
 1  2
 3  4

julia> [1. 2. 3.]
1×3 Matrix{Float64}:
 1.0  2.0  3.0

```

行列 (2次元配列) は, 大括弧 `[]` の中で値を空白と改行で区切って記述する. なお, 改行には セミコロン `;` も使用できる.

多次元配列の例を以下に示す.

```Julia
julia> [1; 2;; 3; 4]
2×2 Matrix{Int64}:
 1  3
 2  4

julia> Float64[1;2;;3;4;;;5;6;7;;8]
2×2×2 Array{Float64, 3}:
[:, :, 1] =
 1.0  3.0
 2.0  4.0

[:, :, 2] =
 5.0  7.0
 6.0  8.0

julia> [1 2; 3 4;;;5 6; 7 8]
2×2×2 Array{Int64, 3}:
[:, :, 1] =
 1  2
 3  4

[:, :, 2] =
 5  6
 7  8

julia> [1 2
       3 4
       ;;;
       5 6
       7 8]
2×2×2 Array{Int64, 3}:
[:, :, 1] =
 1  2
 3  4

[:, :, 2] =
 5  6
 7  8

```

多次元配列の記述方法は, ベクトル (1次元配列) や行列 (2次元配列) と基本的には同じであるが, $n$ 連セミコロンで $n$ 次元配列を記述することができる点が異なる. 具体的には, `;` は第1軸方向の要素列挙, `;;` は第2軸方向の要素列挙, `;;;` は第3軸方向の要素列挙を示しており, これを拡張することで多次元配列を記述することが可能となる.

ベクトル・行列の結合例を以下に示す.

```Julia
julia> [[1 2 3];[4 5 6]]
2×3 Matrix{Int64}:
 1  2  3
 4  5  6

julia> [1:3; [2, 4]; 6]
6-element Vector{Int64}:
 1
 2
 3
 2
 4
 6

julia> [[1 2; 3 4]; [5 6; 7 8]]
4×2 Matrix{Int64}:
 1  2
 3  4
 5  6
 7  8
```

ベクトルの結合は, 大括弧 `[]` の中のベクトルをセミコロン `;` で区切ることで可能となる. また, ベクトルの代わりに範囲リテラルを用いても連結ができる.

なお, サイズに整合性がない場合はエラーが発生する.

```Julia
julia> [[1,2] 3]
ERROR: DimensionMismatch: mismatch in dimension 1 (expected 2 got 1)
Stacktrace:
  [1] _cs
    @ .\abstractarray.jl:1770 [inlined]
  [2] _cshp
    @ .\abstractarray.jl:1766 [inlined]
  [3] _cat_size_shape
    @ .\abstractarray.jl:1746 [inlined]
  [4] cat_size_shape(dims::Tuple{Bool, Bool}, X::Vector{Int64}, tail::Int64)
    @ Base .\abstractarray.jl:1744
  [5] _cat_t(::Val{2}, ::Type{Int64}, ::Vector{Int64}, ::Vararg{Any})
    @ Base .\abstractarray.jl:1785
  [6] _cat(::Val{2}, ::Vector{Int64}, ::Vararg{Union{Number, LinearAlgebra.AbstractTriangular{T, A} where {T, A<:(Matrix)}, LinearAlgebra.Adjoint{<:Any, <:Vector}, LinearAlgebra.Bidiagonal, LinearAlgebra.Diagonal, LinearAlgebra.Hermitian{T, A} where {T, A<:(Matrix)}, LinearAlgebra.SymTridiagonal, LinearAlgebra.Symmetric{T, A} where {T, A<:(Matrix)}, LinearAlgebra.Transpose{<:Any, <:Vector}, LinearAlgebra.Tridiagonal, Matrix, Vector}})
    @ LinearAlgebra C:\Users\yoneyama\AppData\Local\Programs\Julia-1.9.0\share\julia\stdlib\v1.9\LinearAlgebra\src\special.jl:410
  [7] #cat#153
    @ .\abstractarray.jl:1979 [inlined]
  [8] cat
    @ .\abstractarray.jl:1979 [inlined]
  [9] hcat(::Vector{Int64}, ::Int64)
    @ Base .\array.jl:1955
 [10] top-level scope
    @ REPL[126]:1

```

## タプルリテラル / 名前付きタプルリテラル

タプルとは, 複数の値の組を表す言葉である. タプルリテラルの例を以下に示す.

```Julia
julia> (1, 2)
(1, 2)

julia> (1,)
(1,)

julia> ()
()

```

タプルは, 括弧 `()` の中で値をカンマ `,` 区切りにすることで記述する.

名前付きタプルの例を以下に示す.

```Julia
julia> (a = 1, b = 2)
(a = 1, b = 2)

julia> (a = 1,)
(a = 1,)

julia> (;)
NamedTuple()

```

名前付きタプルは, 変数代入のような `(a = 1, b = 2)` のようにして記述する.

## その他のリテラル

|リテラル表記|種類|型|
|---|---|---|
|`true` / `false`|真偽値リテラル|Bool 型|
|`nothing`|nothing リテラル|Nothing 型|
|`missing`|missing リテラル|Missing 型|
|`:ok` 等|シンボルリテラル|Symbol 型|
|`ls` 等|コマンドリテラル|Cmd 型|

### 参考文献

- 後藤俊介, "実践Julia入門", 技術評論社, 2023. ISBN: 978-4-297-13350-4.
