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

## 文字のエンコードについて
詳しくは[Character encoding](https://en.wikipedia.org/wiki/Character_encoding)のTerminology節を参照。
次の節から文字のエンコードの話が始まるため、用語を簡潔に説明する。
- コードポイントとは、\\U0041などそのエンコーディングで許可された整数値を指す。
- コードユニットとは、文字をエンコードするために使われるビット列を指す。

例として、"abc\U10400"を考える。この文字列のUTF-8, 16, 32のコードユニットは以下の通り。

| エンコーディング | コードユニット                            |
| -------------- | ---------------------------------------- |
| UTF-32         | (00000061, 00000062, 00000063, 00010400) |
| UTF-16         | (0061, 0062, 0063, d801, dc00)           |
| UTF-8          | (61, 62, 63, f0, 90, 90, 80)             |

コードポイント数は全て4である。

## ユニコードとUTF-8
文字列リテラルはUTF-8でエンコードされる。

文字列のインデックスはコードユニット単位(UTF-8の場合はバイト単位)なので、有効でないバイトを指定してしまう可能性がある。その場合、例外が投げられる。
```
> s = "\u2200 x \u2203 y"
"∀ x ∃ y"

> s[1]
'∀': Unicode U+2200 (category Sm: Symbol, math)

> s[2]
ERROR: StringIndexError: invalid index [2], valid nearby indices [1]=>'∀', [4]=>' '
```

nextind(s, 1)で文字列sのインデックス1の次の文字のインデックスをとれる。前はprevind関数。
```
> nextind(s, 1)
4

> s[prevind(s, end, 2)]    # 最後の2は2つ前という意味
'∃': Unicode U+2203 (category Sm: Symbol, math)

> prevind(s, lastindex(s))
10
```
```
