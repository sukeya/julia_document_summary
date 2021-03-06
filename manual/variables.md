# 変数
変数xに1を入れる(宣言と初期化を同時に行う)。
```
> x = 1
1
```
この方法ではxの型は固定されないので、xに文字列も入れられる。
```
> x = "Hello, Worker"
"Hello, Worker"
```
Juliaの名前は大文字、小文字を区別し、名前で変数を特別扱いしない。
UTF-8の文字を名前に使える。

"(LaTeXの入力方法) + tab"で記号を入力できる。
```
> \delta(tabキー) => δ
```
入力方法がわからない文字はREPL(Juliaのインタラクティブな実行環境)で、"?"の後にその文字を貼り付ければわかる。

(非推奨) 必要ならビルトイン定数と関数を再定義できる。ただし、再定義する前にその定数か関数を使った場合、再定義すると例外が発生する。

## 使える変数名
変数名は半角アルファベット(a-zA-Z)、アンダースコア(\_\)、いくつかのユニコード(00A0より大きい)で始めなければならない。
2文字目以降は"!"や数字、その他のユニコードを使うことができる。

"+"のような演算子は有効な名前だが、前後に丸括弧が必要。
```
> (+) = f
```
とすると演算子+に関数fが代入される。

"⊗"のようなカテゴリーSmのユニコードのほとんどは演算子として解釈され、ユーザー定義メソッド(メソッドの章を参照)に利用できる。

関数にマーク、プライム、上下の添え字をつけることができ、演算子の優先順位は元の関数と変わらない(例　+̂ₐ″)。
上か下に添え字がついた演算子は後ろの変数と区別するために半角スペースが必要。

アンダースコアだけの変数は値を代入できるが、取得できない
```
> x, ___ = size([2 2; 1 1])
(2, 2)

> y = ___
ERROR: syntax: all-underscore identifier used as rvalue
```
明示的に変数に使えない名前はビルトインキーワードだけ。

(要確認) Juliaではいくつかのユニコードは同じ文字として扱われる(Julia 1.7.1では確認できず)。
```
> ɛ = 3
3

> µ
3
```
