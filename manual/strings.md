# 文字列
Juliaの具体的な文字列型はString。
String型はUTF-8で全てのユニコードをサポートする(transcode関数で他のユニコードエンコーディングへ/から変換できる)。

全ての文字列型は抽象型AbstractStringのサブタイプである。
外部パッケージはAbstractStringのサブタイプを追加できる。

文字列を引数にとる関数を定義するとき、任意の文字列型を受け入れるためにAbstractStringで宣言すべきである。
1文字を表すAbstractChar型がある。AbstractCharのビルトインのサブタイプChar型はUTF-8に基づいた、任意のユニコード文字を表せる32ビットのプリミティブ型である。

文字列は不変。

文字列はバイト単位のインデックスから文字を返す。あるインデックスに対しては返す文字がないので、代わりに例外が投げられる。

## 文字
Char型は'で囲むと作られる。文字列は"で囲む。
```
> 'x'
'x': ASCII/Unicode U+0078 (category Ll: Letter, lowercase)
```
Char型を簡単にコードポイントに変換できる。ただし、変換後の整数はIntなのでビット数に依存する。
```
> Int('x')
120
```
全ての整数が有効なユニコードコードポイントではないが、JuliaではパフォーマンスのためChar型を整数から作る時に有効かどうか確認しない。
```
> Char(0x110000)
'\U110000': Unicode U+110000 (category In: Invalid, too high)
```
\\uの後に最大4桁の16進数を書くか、\\Uの後に最大8桁の16進数を書いて'で囲めばユニコードを直接指定できる。
```
> '\u0', '\u2200', '\U10ffff'
('\0', '∀', '\U10ffff')
```
Cのエスケープ文字も使える。
```
> '\t', '\n'
('\t', '\n')
```
Cのように文字を整数のように使える。
```
> 'A' < 'a', 'x' - 'a', 'A' + 1
(true, 23, 'B')
```

## 文字列の基礎
文字列リテラルは"か"""で区切る。
```
> "Hello, worker.\n"
"Hello, worker.\n"

> """Contains "quote" characters"""
"Contains \"quote\" characters"
```

バックスラッシュを使うと、次の行に続けて文字列を書ける。
```
> "This is a long \
  line"
"This is a long line"
```
文字列から文字を取り出すにはインデックスを指定する。
```
> str = "Hello, worker.\n";

> str[begin]
'H'

> str[1]
'H'

> str[6]
','

> str[end]
'\n'
```
begin, endは与えられた次元でそれぞれ最初と最後のインデックスを表す。ただし、[]の中でしか使えない。

**Juliaのインデックスは1始まり。**

文字列の最後のインデックスは文字列の長さと一致するとは限らないことに注意(マルチバイト文字があると一致しない)。

文字列に限らず、firstindex, lastindex関数でそれぞれ最初と最後のインデックスをとれる。

文字列に限らず、length関数で長さをとれる。
```
> test = "てすと";

> lastindex(test)
7

> length(test)
3

> firstindex(test)
1
```

begin, endを値のように四則演算したりしてもよい。
```
> str = "1234"; str[end ÷ 2]
'2'
```
閉区間\[begin, end\]の外へのアクセスは例外を投げる。
```
> str[begin - 1]
ERROR: BoundsError: attempt to access 4-codeunit String at index [0]
```
\[a:b\]でインデックスa以上b**以下**の部分文字列をとれる。
```
> str = "Hello, worker.\n";

> str[4:9]
"lo, wo"

> str[4:4] # string
"l"

> str[4] # char
'l'
```
\[a:b\]は文字列で指定された範囲でコピーする。SubString型を使うと、コピーではなくビューを作成する。
```
> str = "long string";

> substr = SubString(str, 1, 4)
"long"

> typeof(substr)
SubString{String}
```

