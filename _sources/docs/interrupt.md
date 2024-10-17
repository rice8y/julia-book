# 実行の中断

VScode で Julia の実行を中断するコマンドとして, Julia 拡張機能 (Julia-VScode) の `language-julia.interrupt` が存在する. このコマンドのキーバインドは, `Ctrl+C` と `Ctrl+Shift+C` であり, それぞれの有効化条件は以下の通りである.

|キー|有効化条件|
|---|---|
|Ctrl+C|ターミナルにフォーカスがあり, かつ, REPLがアクティブであり, かつ, Julia が評価処理を行っているとき|
|Ctrl+Shift+C|Julia が評価処理を行っているとき|

なお, これらについては Julia-VScode の [`package.json`](https://github.com/julia-vscode/julia-vscode/blob/main/package.json) に記述されている. `package.json` から `language-julia.interrupt` に関する部分を抜粋して以下に示す.

```json
{
    "command": "language-julia.interrupt",
    "title": "Julia: Interrupt Execution",
    "icon": "$(debug-pause)",
    "enablement": "julia.isEvaluating"
},
{
    "command": "language-julia.interrupt",
    "key": "Ctrl+C",
    "when": "terminalFocus && julia.isActiveREPL && julia.isEvaluating"
},
{
    "key": "Ctrl+Shift+C",
    "command": "language-julia.interrupt",
    "when": "julia.isEvaluating"
},
```

Julia-VScode が提供する他の機能についても [`package.json`](https://github.com/julia-vscode/julia-vscode/blob/main/package.json) に記述されているため, 時間があるときに覗いてみたい. (多分覗くことはない...)
