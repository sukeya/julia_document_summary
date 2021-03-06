# 複素数と有理数
## 複素数
imは√-1。i、jはインデックスとして使うため、√-1ではない。

imは数値リテラルというより変数に近い。
```
> 1 + 3/4im
1.0 - 0.75im
```
これは4imが数値リテラル係数と判断されたため。

初等関数は複素数上でも有理数上でも定義されている。ただし、実数から実数、複素数から複素数のどちらかで実数から複素数はない。
```
> sqrt(-1)
ERROR: DomainError with -1.0:
sqrt will only return a complex result if called with a complex argument. Try sqrt(Complex(x)).

> sqrt(-1 + 0im)
0.0 + 1.0im
```

複素数の作成時に数値リテラル係数は使えない。そのため、掛け算\*を明示する必要がある。
```
> a = 2; b = 3; a + b * im
2 + 3im
```
このやり方はおすすめしない。代わりにcomplex関数を使うべき。
```
> a = 2; b = 3; complex(a, b)
2 + 3im
```
Inf、NaNを実部、虚部に使うことが出来る。
```
> 1 + Inf * im, 1 + NaN * im
(1.0 + Inf*im, 1.0 + NaN*im)
```

## 分数
分数は"//"で作成できる。分子、分母は自動的に約分される。分子か分母のどちらかが0は可。両方が0はエラー。

他の型を混ぜても使える。
```
> 2 // 3 + 1, 2 // 3 * (2 + 3im), 2 // 3 * 1.5
(5//3, 4//3 + 2//1*im, 1.0)
```

分子はnumerator関数で、分母はdenominator関数で取得できる。
```
> numerator(6//9)
2

> denominator(6//9)
3
```
