# エラトステネスの篩

## v1

```Julia
function sieve(x::Int)::Vector{Int}
    flags = trues(x)
    flags[1] = false
    sqrt_x = Int(ceil(sqrt(x) + 0.1))
    for p in 2:sqrt_x
        if flags[p]
            for mult in p*p:p:x
                @inbounds flags[mult] = false
            end
        end
    end
    return findall(flags)
end
```

### REPL

```bash
julia> @time sieve(10^9)
  5.204464 seconds (5 allocations: 507.145 MiB, 0.13% gc time)
50847534-element Vector{Int64}:
         2
         ⋮
 999999937
```

## v2

```Julia
function sieve(x::Int)::Vector{Int}
    flags = trues(div(x-1, 2))
    sqrt_x = ceil(sqrt(x) + 0.1)
    sqrt_xi = div(sqrt_x, 2)
    for i in 1:Int(sqrt_xi)
        if !flags[i]
            continue
        end
        p = 2 * i + 1
        for mult in (2*i*(i+1)):p:length(flags)
            @inbounds flags[mult] = false
        end
    end
    return insert!(2 * findall(flags) .+ 1, 1, 2)
end
```

### REPL

```bash
julia> @time sieve(10^9)
  2.727884 seconds (10 allocations: 1.367 GiB, 2.07% gc time)
50847534-element Vector{Int64}:
         2
         ⋮
 999999937
```
