# 変数

Julia の型システムは, 動的型付けであるため型の宣言は不要である.

```Julia
julia> x = 10
10

julia> typeof(x)
Int64

julia> y = "abcdefg"
"abcdefg"

julia> typeof(y)
String
```

REPL や VScode では, `\+LaTeXのシンボル名+Tab` で LaTeX のシンボル名を Unicode の数学記号に自動補完してくれる.

![Gamma](../_images/gamma.gif)

なお, Juliaでは明示的に型宣言を行うこともできる. 型宣言には `::` 演算子を用いる. 詳しくは[型](j_basic_td.nd)に示す.

```Julia
julia> x::Int64 = 1
1

julia> typeof(x)
Int64
```

また, 組み込みの定数や関数を再定義することもできる. (組み込みの予約語の再定義はできない. )

```Julia
julia> pi = 3
3

julia> pi
3

julia> sqrt = 4
4

julia> sqrt
4
```

しかし, 組み込みの定義や関数を再定義する前に既に利用している場合は, 再定義しようとするエラーが発生する.

```Julia
julia> pi
π = 3.1415926535897...

julia> pi = 3
ERROR: cannot assign a value to imported variable MathConstants.pi from module Main
Stacktrace:
 [1] top-level scope
   @ REPL[6]:1

julia> sqrt(100)
10.0

julia> sqrt = 4
ERROR: cannot assign a value to imported variable Base.sqrt from module Main
Stacktrace:
 [1] top-level scope
   @ REPL[8]:1
```

最も, 組み込みの定義や関数を再定義する必要などほとんどないと思うので推奨はしない.
