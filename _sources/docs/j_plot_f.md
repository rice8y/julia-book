# Plots によるグラフ描画

```{note}
グラフの描画方法は, 扱うデータや目的により様々であるため, ここでは一部の方法しか取り上げない. 
```

## 準備

```Julia
using Plots, LaTeXStrings
plot_font = "Computer Modern"
default(fontfamily=plot_font, linewidth=1, label=nothing, grid=true)
```

## グラフ描画の基礎

例として, $y=\sin x$ を $0\leq x\leq\pi$ の範囲で描画する.

```Julia
x = 0:0.01:π
y = sin.(x)
plot(x, y)
```

![sample01](../_images/sample_plot01.png)

上記のグラフにラベルを付けるには以下のようにする.

```Julia
x = 0:0.01:π
y = sin.(x)
plot(x, y, xlabel=L"x", ylabel=L"y", label=L"y=\sin x", title="sample plot", dpi=300)
```

![sampl02](../_images/sample_plot02.png)

`xlabel` , `ylabel` で $x$, $y$ 軸のラベルを設定する. `label` ではグラフのラベル, `title` ではタイトルを設定する. また, `dpi` は解像度を設定するパラメータである.

## 複数グラフの結合

複数のグラフを結合するには以下のようにする.

```Julia
x = range(0, 10, length=100)
y1 = @. exp(-0.1x) * cos(4x)
y2 = @. exp(-0.3x) * cos(4x)
y3 = @. exp(-0.5x) * cos(4x)
plot(x, [y1 y2 y3], layout=(3, 1), legend=false, dpi=300)
```

![multi](../_images/multi.png)

こんな方法もある.

```Julia
x = range(0, 10, length=100)
y1 = @. exp(-0.1x) * cos(4x)
y2 = @. exp(-0.3x) * cos(4x)
y3 = @. exp(-0.5x) * cos(4x)
plot(
    plot(x, y1),
    plot(x, y2),
    plot(x, y3),
    layout=(3, 1),
    legend=false,
    dpi=300
)
```

![multi](../_images/multi.png)

得られる出力はどちらでも同じ.

```{note}
執筆中 (2024/06/17 時点)
```
