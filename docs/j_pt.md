# パラメトリック型

`Number` の直接の派生型には, `Real` と `Complex` が存在する.

```Julia
julia> subtypes(Number)
2-element Vector{Any}:
 Complex
 Real

```

ここで, `Real` は抽象型である.

```Julia
julia> isabstracttype(Real)
true

```

`Complex` についても同様に型を調べてみる.

```Julia
julia> isabstracttype(Complex)
false

julia> isconcretetype(Complex)
false

```

すると, `Complex` は抽象型でも具象型でもないことが分かる. ここで, 複素数の値を用いてその型を調べてみる.

```Julia
julia> typeof(im)
Complex{Bool}

julia> typeof(1 + im)
Complex{Int64}

julia> typeof(1.0 + 1.0im)
ComplexF64 (alias for Complex{Float64})

```

`Comlex{Bool}` , `Complex{Int64}` が返ってきた. ここで, `Complex{}` のうち, `{}` に入るものを型パラメータ, 型パラメータの指定ができる型(ここでは `Complex`)をパラメトリック型という.

```Julia
julia> isconcretetype(Complex)
false

julia> isconcretetype(Complex{Int})
true

```

また, 上記の例より, 型パラメータを指定すると `Complex` は具象型になることが分かる. つまり, パラメトリック型は型パラメータを指定すると具象型となるのである.

## UnionAll 型

ある型がパラメトリック型であるかを確認する方法として, `typeof()` の値を見る方法がある.

```Julia
julia> typeof(Int)
DataType

julia> typeof(Complex)
UnionAll

julia> typeof(Complex{Int})
DataType

```

上記の例より, 型パラメータを取らない型の場合は, `DataType` が返り, パラメトリック型の場合は, 型パラメータが指定されていなければ `UnionAll` , 型パラメータが指定されていれば `DataType` が返ってくることが分かる. ここで, `typeof()` で `UnionAll` が返る型を UnionAll型という. `Complex` は パラメトリック型であるが, より正確には UnionAll 型である.

## パラメトリック抽象型

```Julia
julia> isabstracttype(Array)
false

julia> typeof(Array)
UnionAll

julia> Array |> supertype |> supertype
AbstractArray

julia> isabstracttype(AbstractArray)
true

julia> typeof(AbstractArray)
UnionAll

```

上記の例より, `Array` は 抽象型ではなく UnionAll 型であるが, `Array` の基本型の基本型である `AbstractArray` は抽象型であり UnionAll 型であることが分かる. このように, パラメトリック型でありかつ抽象型であるような型を, パラメトリック抽象型という.

### 参考文献

- 後藤俊介, "実践Julia入門", 技術評論社, 2023. ISBN: 978-4-297-13350-4.
