---
title: "標準 ベイズ統計学 第3章 演習問題 解答例"
emoji: "🌏"
type: "idea"
topics:
  - "ベイズ統計学"
published: true
published_at: "2026-04-04 16:18"
---

https://zenn.dev/honwakasan/articles/e149866170f3f0 の第3章の解答例です.

## 演習問題 3.1

### (a)
$Y_i \sim Bin(1, \theta)$ とする. 仮定より, 条件付き独立性が成り立っているので次のように変形できる.

$$
\begin{aligned}
P(Y_1 = y_1, \cdots, Y_{100} = y_{100} \mid \ \theta) &= \prod_{i= 1}^{100} P(Y_i = y_i \mid \ \theta) \\

&= \prod_{i= 1}^{100} \theta^{y_i} (1 - \theta)^{1 - y_i} \\

&= \theta^{\sum_{i = 1}^n y_i} (1 - \theta)^{n - \sum_{i = 1}^n y_i} 

\end{aligned}
$$

である. また, $Y_i \sim Bin(1, \theta)$ なので, $\ \sum_{i=1}^n Y_i \sim Bin(100, \theta)$ である. 故に

$$
P \left( \sum_{i=1}^{100} Y_i = y \mid \theta \right) = {}_{100}C_y \theta^y (1 - \theta)^{100 - y}  $$

である.

### (b)
以下のコードにより, 二項分布を出力する.
```python
import numpy as np
import pandas as pd
import japanize_matplotlib
import matplotlib.pyplot as plt
import seaborn as sns
import math

plt.style.use("ggplot")

# 二項分布の確率関数
def binom_dist(theta, n, y):
    return math.comb(n, y) * (theta**y) * ((1 - theta)**(n - y))

# θ = 0 ~ 1 を 0.1刻みで生成
theta_vals = np.arange(0, 1.01, 0.1)

# 値を計算
y_vals = [binom_dist(theta, 100, 57) for theta in theta_vals]

# 棒グラフ
plt.style.use("ggplot")
plt.bar(theta_vals, y_vals, width=0.08)  # 幅を少し狭くすると見やすい

plt.xlabel(r"$\theta$")
plt.ylabel("確率")
plt.title(r"各 $\theta$ ごとの二項分布の確率関数 $(n=100, y=57)$")
plt.savefig("3-1-a.png")
plt.show()
```
以下の描画結果を得る. 
![](https://storage.googleapis.com/zenn-user-upload/2f068f92e853-20260404.png)

### (c)
事後分布を求める. $P(\theta)$ が一定であることを考慮すると

$$
\begin{aligned}
P \left(\theta\ \middle |\ \sum_{i = 1}^n Y_i = y \right) 

&= \frac{P \left(\sum_{i = 1}^n Y_i = y \ \middle |\ \theta\  \right) P(\theta)}{ P \left(\sum_{i = 1}^n Y_i = y  \right) } \\

&= \frac{P \left(\sum_{i = 1}^n Y_i = y \ \middle |\ \theta\  \right) P(\theta)}{ \sum_{\theta} P \left(\sum_{i = 1}^n Y_i = y  \mid \theta \right) P(\theta)} \\

&= \frac{P \left(\sum_{i = 1}^n Y_i = y \ \middle |\ \theta\  \right)}{ \sum_{\theta} P \left(\sum_{i = 1}^n Y_i = y  \mid \theta \right) } \\
\end{aligned}
$$

となる. $\ y = 57, n = 100, \theta \in \{ 0, 0.1, 0.2, \ldots, 1.0\}$ として, 事後分布の値およびその描画結果は以下のコードで出力できる.
```python
# 二項分布の確率関数
def binom_dist(theta, n, y):
    return math.comb(n, y) * (theta**y) * ((1 - theta)**(n - y))

# θ = 0 ~ 1 を 0.1刻みで生成
theta_vals = np.arange(0, 1.01, 0.1)

# 値を計算
y_vals = [binom_dist(theta, 100, 57) for theta in theta_vals]

# 分母
bunbo = sum(y_vals)

# 棒グラフ
plt.style.use("ggplot")
plt.bar(theta_vals, y_vals/bunbo, width=0.08)  # 幅を少し狭くすると見やすい

plt.xlabel(r"$\theta$")
plt.ylabel("確率")
plt.title(r"各 $\theta$ ごとの二項分布の確率関数 $(n=100, y=57)$")
plt.savefig("3-1-c.png")
plt.show()
```

![](https://storage.googleapis.com/zenn-user-upload/dbb7cce01364-20260404.png)

また, 以下のコードにより各 $\theta$ ごとの値も手に入る.

```python
pd.DataFrame(
    {
        r"$\theta$":theta_vals,
        r"P($\theta \mid \sum_{i = 1}^{100} Y_i = 57$)":y_vals/bunbo
    }
)
```

| index | $θ$   | $P(θ \mid Σ_{i=1}^{100} Y_i = 57)$ |
|------:|-----|-----------------------------|
| 0     | 0.0 | 0.000000e+00               |
| 1     | 0.1 | 4.153701e-30               |
| 2     | 0.2 | 3.780824e-15               |
| 3     | 0.3 | 1.321705e-07               |
| 4     | 0.4 | 2.311695e-03               |
| 5     | 0.5 | 3.040939e-01               |
| 6     | 0.6 | 6.748515e-01               |
| 7     | 0.7 | 1.874172e-02               |
| 8     | 0.8 | 1.014907e-06               |
| 9     | 0.9 | 9.502335e-17               |
| 10    | 1.0 | 0.000000e+00               |

### (d)
$P(\theta) = 1$ より

$$
\begin{aligned}
P(\theta) P \left(\sum_{i = 1}^{100} Y_i = 57 \ \middle |\ \theta\  \right) 

&=  P \left(\sum_{i = 1}^{100} Y_i = 57 \ \middle |\ \theta\  \right) \\

&= {}_{100} C_{57} \theta^{57} (1 - \theta)^{43} 
\end{aligned}
$$

である. これを任意の $\theta \in [0, 1]$ に対して描画する.

```python
# θ = 0 ~ 1 を 0.0001刻みで生成
theta_vals = np.arange(0, 1.01, 0.0001)

# 値を計算
y_vals = [binom_dist(theta, 100, 57) for theta in theta_vals]

# 棒グラフ
plt.style.use("ggplot")
plt.plot(theta_vals, y_vals)  # 幅を少し狭くすると見やすい

plt.xlabel(r"$\theta$")
plt.ylabel("確率")
plt.title(r"各 $\theta$ ごとの二項分布の確率関数 $(n=100, y=57)$")
plt.savefig("3-1-d.png")
plt.show()
```
描画結果は以下である.
![](https://storage.googleapis.com/zenn-user-upload/82f8534c8d84-20260404.png)

### (e)
以下のコードで, 事後分布を可視化する.
```python
theta_vals = np.arange(0, 1.01, 0.0001)

# 値を計算
y_vals = [beta.pdf(theta, 58, 44) for theta in theta_vals]

# 棒グラフ
plt.style.use("ggplot")
plt.plot(theta_vals, y_vals)

plt.xlabel(r"$\theta$")
plt.ylabel("確率")
plt.title(r"事後分布の密度関数 $(\alpha=58, \beta=44)$")
plt.savefig("3-1-e.png")
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/858f49783ac2-20260404.png)

## 演習問題3.2
### (a)
以下で描画用の関数を作成しておく.
```python
import numpy as np
import pandas as pd
from scipy.stats import beta
from scipy.stats import binom
import matplotlib.pyplot as plt

plt.style.use("ggplot")

def subplots_distribution(ax, x, y, title):

    ax.plot(x, y)
    ax.set_xlabel(r"$\theta$")
    ax.set_ylabel("CDF")
    ax.set_title(title)
    ax.grid(True)
```
パラメータは以下.
```python
# パラメータ
a = 2 
b = 8 
n = 43 # 投獄された人数
y = 15 # 再犯者数
```
$p(\theta),\, p(y \mid \theta),\, p(\theta \mid y)$ を以下で描画する.
```python
fig, axes = plt.subplots(1, 3, figsize=(15, 4))

x = np.linspace(0, 1, 200)
title = fr"$p(\theta)$ (a={a}, b={b})"
cdf = beta.pdf(x, a, b)
subplots_distribution(axes[0], x, cdf, title)

p = np.linspace(0, 1, 500)
cdf = binom.pmf(y, n, p)
title = fr"$p(y \mid \theta)$ (n = {n}, y={y})" 
subplots_distribution(axes[1], p, cdf, title)

title = fr"$p(\theta \mid y)$ (a = {a}, b = {b}, n = {n}, y = {y})"
cdf = beta.pdf(x, y + a, n - y + b)
subplots_distribution(axes[2], x, cdf, title)

plt.tight_layout()
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/118691adc6d1-20260417.png)

事後期待値, 事後モード, 事後標準偏差は以下で算出する.
```python
E_post = (a + y) / (a + b + n)
mode_post = (a + y - 1) / (a + b + n - 2)
std_var= np.sqrt((E_post * (1 - E_post)) / (a + b + n + 1))

print("事後期待値 : ", E_post)
print("事後モード : ", mode_post)
print("事後標準偏差 : ", float(std_var))
print("95%ベイズ信用区間：", beta.ppf(np.array([0.025, 0.975]), a + y, b + n - y))
```
結果は以下. 
```python
事後期待値 :  0.32075471698113206
事後モード :  0.3137254901960784
事後標準偏差 :  0.0635188989834093
95%ベイズ信用区間： [0.20329779 0.45102398]
```

### (b)
$a = 8,\, b = 2$ の時の結果は以下で出力する.
```python
# パラメータ
a = 8
b = 2
n = 43
y = 15

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

x = np.linspace(0, 1, 200)
title = fr"$p(\theta)$ (a={a}, b={b})"
cdf = beta.pdf(x, a, b)
subplots_distribution(axes[0], x, cdf, title)

p = np.linspace(0, 1, 500)
cdf = binom.pmf(y, n, p)
title = fr"$p(y \mid \theta)$ (n = {n}, y={y})" 
subplots_distribution(axes[1], p, cdf, title)

title = fr"$p(\theta \mid y)$ (a = {a}, b = {b}, n = {n}, y = {y})"
cdf = beta.pdf(x, y + a, n - y + b)
subplots_distribution(axes[2], x, cdf, title)

plt.tight_layout()
plt.show()
```
![](https://storage.googleapis.com/zenn-user-upload/75a254bd7561-20260417.png)

統計量の算出は以下.
```python
E_post = (a + y) / (a + b + n)
mode_post = (a + y - 1) / (a + b + n - 2)
std_var= np.sqrt((E_post * (1 - E_post)) / (a + b + n + 1))

print("事後期待値 : ", E_post)
print("事後モード : ", mode_post)
print("事後標準偏差 : ", float(std_var))
print("95%ベイズ信用区間：", beta.ppf(np.array([0.025, 0.975]), a + y, b + n - y))
```

```python
事後期待値 :  0.4339622641509434
事後モード :  0.43137254901960786
事後標準偏差 :  0.06744531631926798
95%ベイズ信用区間： [0.30469562 0.5679528 ]
```

### (c)
```python
x = np.linspace(0, 1, 200)

a = 2; b = 8
title_2_8 = fr"$p(\theta)$ (a={a}, b={b})"
cdf_2_8 = beta.pdf(x, a, b)

a = 8; b = 2
title_8_2 = fr"$p(\theta)$ (a={a}, b={b})"
cdf_8_2= beta.pdf(x, a, b)

title = fr"$p(\theta)$, mixture beta(2, 8) and beta(8, 2)"
```

(a), (b) はともに, $\theta = 0.4$ くらいのところで山がある. 一方, 下記の一番右の画像を見ると, `0.1` ~ `0.2` のところと, `0.8` ~ `0.9` のところに山がある. これは, 再犯率には, 低い場合と高い場合で二極化していることを意味している. 
![](https://storage.googleapis.com/zenn-user-upload/37d1f8a43e46-20260417.png)

### (d)
#### (1)

$p(\theta)\, p(y \mid \theta)$ を求めると

$$
\begin{aligned}
p(\theta) p(y \mid \theta)
 
 &= \frac{1}{4} \frac{\Gamma(10)}{\Gamma(2)\Gamma(8)} (3 \theta(1 - \theta)^7 + \theta^7 (1 - \theta))\, \cdot \, {}_{43}C_{15} \theta^{15} (1 - \theta)^{43 - 15} \\

 &= \frac{1}{4} \frac{\Gamma(10)}{\Gamma(2)\Gamma(8)} \frac{\Gamma(44)}{\Gamma(16)\Gamma(29)} \left(3 \theta(1 - \theta)^7 + \theta^7 (1 - \theta) \right)\, \cdot \, \theta^{15} (1 - \theta)^{28}  \\

 &= \frac{1}{4} \frac{\Gamma(10)}{\Gamma(2)\Gamma(8)} \frac{\Gamma(44)}{\Gamma(16)\Gamma(29)} \left(3 \theta^{16} (1 - \theta)^{35} + \theta^{22} (1 - \theta)^{29} \right)
\end{aligned}
$$

となる. 

さらに, 事後分布 $p(\theta \mid y)$ を求めることを考える. 混合事前分布 $p(\theta)$ が

$$
p(\theta) = \frac{1}{4}\frac{\Gamma(10)}{\Gamma(2)\Gamma(8)}\left(3\theta(1- \theta)^7 + \theta^7 (1- \theta) \right)
$$

であり, 反応変数の分布 $p(y \mid \theta)$ は

$$
p(y \mid \theta) = \frac{\Gamma(44)}{\Gamma(16)\Gamma(29)} \theta^{15}(1- \theta)^{28}
$$

である. 

$$
\begin{aligned}
\int_0^1 \theta^{16}(1 - \theta)^{35} d\theta = \frac{\Gamma(17)\Gamma(36)}{\Gamma(53)}, \quad
\int_0^1 \theta^{22}(1 - \theta)^{29} d\theta = \frac{\Gamma(23)\Gamma(30)}{\Gamma(53)}
\end{aligned}
$$

なので

$$
\begin{aligned}
\int_0^1p(y\mid\theta) p(\theta) d\theta 

    &= \frac{3}{4}\frac{\Gamma(10)}{\Gamma(2)\Gamma(8)}\frac{\Gamma(36)\Gamma(17)}{\Gamma(53)} + \frac{1}{4}\frac{\Gamma(10)}{\Gamma(2)\Gamma(8)}\frac{\Gamma(23)\Gamma(30)}{\Gamma(53)}
\end{aligned}
$$

を得る. 

$$
\begin{aligned}
p(y\mid\theta) p(\theta)

    &= \frac{1}{4}\frac{\Gamma(10)}{\Gamma(2)\Gamma(8)}\frac{\Gamma(44)}{\Gamma(16)\Gamma(29)} 
    \left( 3\theta(1 - \theta)^7 + \theta (1 - \theta)^7 \right)\theta^{15}(1 - \theta)^{28}
\end{aligned}
$$

であるので, 事後分布 $p(\theta \mid y)$ は

$$
\begin{aligned}
p(\theta \mid y) 

    &= \frac{p(y\mid\theta) p(\theta)}{
\int_0^1p(y\mid\theta) p(\theta) d\theta} \\

    &= \frac{\displaystyle\frac{1}{4}\frac{\Gamma(44)}{\Gamma(16)\Gamma(29)} \left(3\theta^{16}(1- \theta)^{35} + \theta^{22}(1- \theta)^{29} \right)}{\displaystyle\frac{3}{4}\frac{\Gamma(36)\Gamma(17)}{\Gamma(53)} + \frac{1}{4}\frac{\Gamma(23)\Gamma(30)}{\Gamma(53)}}
\end{aligned}
$$

となる.

#### (2)
$(1)$ より, 第一項が $Beta(17, 36)$ に従い, 第二項が $Beta(23, 30)$ に従う.

#### (3)
pythonで, $(1)$ の関数 $p(\theta)\, p(y \mid \theta)$ を描画するコードは以下.
```python
from scipy.special import gammaln

# θの範囲
theta = np.linspace(0, 1, 500)

# 定数部分（logで計算）
log_const = (
    np.log(1/4)
    + gammaln(10) - gammaln(2) - gammaln(8)
    + gammaln(44) - gammaln(16) - gammaln(29)
)

const = np.exp(log_const)

# 関数本体
y = const * (
    3 * theta**16 * (1 - theta)**35
    + theta**22 * (1 - theta)**29
)

# プロット
plt.figure()
plt.plot(theta, y)
plt.xlabel(r"$\theta$")
plt.ylabel(r"$p(\theta)\, p(y \mid \theta)$")
plt.title(r"$p(\theta)\, p(y \mid \theta)$")
plt.grid(True)
plt.show()
```
出力結果は以下である. 
![](https://storage.googleapis.com/zenn-user-upload/a3cffc74adde-20260417.png)

## 演習問題3.3
### (a)
#### 事後分布
一般に $i = 1,\dots, m$ に対し

$$
\begin{aligned}
\theta &\sim gamma(a,\, b) \\
y_i \mid \theta &\sim Po(\theta)
\end{aligned}
$$

のとき $\theta$ の事後分布 $\theta \mid y_1, \dots, y_m$ は, 条件付き独立性を用いることで

$$
\begin{aligned}
P(\theta \mid y_{1}, \dots, y_{m}) &\propto P(y_{1}, \dots, y_{m} \mid \theta) P (\theta) \\

 &= \left(\prod_{i = 1}^m P(y_{i} \mid \theta) \right) P(\theta) \\

 &= \left(\prod_{i = 1}^m \frac{\theta^{y_{i}}}{y_{i} !}e^{-\theta} \right) \frac{b^{a}}{\Gamma(a)} \theta^{a-1} e^{-b \theta} \\

 &\propto \theta_A^{\sum_{i = 1}^m y_{i}} e^{-m \theta} \theta^{a-1} e^{-b\, \theta} \\

 &= \theta^{\sum_{i = 1}^m y_{i} + a - 1} e^{-(m + b)\, \theta}

\end{aligned}
$$

となる. 従って

$$
\theta \mid y_{1}, \dots, y_{m} \sim gamma \left(\sum_{i = 1}^m y_{i} + a,\, m + b \right)
$$

という事後分布を得る. これを $\theta_A$ や $\theta_B$ の条件に当てはめていけば良い. 問題文より

$$\sum_{i = 1}^m y_{Ai} = 117,\quad m = 10,\quad \theta_A \sim gamma(120, 10) $$

なので

$$
\theta_A \mid y_{A1}, \dots, y_{Am} \sim gamma(237, 20)
$$

である. 同様にして, $\theta_B$ について, 問題文より, 

$$\sum_{i = 1}^n y_{Bi} = 113,\quad n = 13,\quad \theta_B \sim gamma(12, 1) $$

であるので, 事後分布は

$$
\theta_B \mid y_{B1}, \dots, y_{Bm} \sim gamma(125, 14)
$$

となる.

#### 事後平均
$\theta \sim gamma(a, b)$ のときの期待値は

$$
E[\theta] = \frac{a}{b}
$$

だったことを思い出すと

$$
\theta_A \mid y_{A1}, \dots, y_{Am} \sim gamma(237, 20), \quad \theta_B \mid y_{B1}, \dots, y_{Bm} \sim gamma(125, 14)
$$

なので

$$
E[\theta_A] = \frac{237}{20}, \quad E[\theta_A] = \frac{125}{14}
$$

である.

#### 事後分散
$\theta \sim gamma(a, b)$ のときの分散は

$$
Var(\theta) = \frac{a}{b^2}
$$

だったことを思い出すと

$$
\theta_A \mid y_{A1}, \dots, y_{Am} \sim gamma(237, 20), \quad \theta_B \mid y_{B1}, \dots, y_{Bm} \sim gamma(125, 14)
$$

なので

$$
Var(\theta_A) = \frac{237}{400}, \quad Var(\theta_B) = \frac{125}{196}
$$

である.

#### 事後モード
$\theta \sim gamma(a, b)$ のときのmodeは

$$
\operatorname{mode}(\theta) =
\begin{cases}
\dfrac{a - 1}{b} & (a > 1), \\
0                & (a \le 1).
\end{cases}
$$

だったことを思い出すと

$$
\theta_A \mid y_{A1}, \dots, y_{Am} \sim gamma(237, 20), \quad \theta_B \mid y_{B1}, \dots, y_{Bm} \sim gamma(125, 14)
$$

なので

$$
mode (\theta_A) = \frac{59}{5}, \quad mode(\theta_B) = \frac{63}{7}
$$

である.

#### 分位点に基づく95%信用区間
事後分布の $100(1-\alpha)$%信用区間を求めるには

$$
\begin{aligned}
P(\theta < \theta_{\alpha/2} \mid Y = y) &= \alpha / 2 \\
P(\theta > \theta_{1 -\alpha/2} \mid Y = y) &= \alpha / 2 \\
\theta_{\alpha/2} &< \theta_{1-\alpha/2}
\end{aligned}
$$

を満たす $\theta_{\alpha / 2},\, \theta_{1 -\alpha / 2}$ を求めれば良い. 

$\theta_A \mid y_{A1}, \dots, y_{Am} \sim gamma(237, 20)$ より, Pythonで以下のようにして求める.

```python
from scipy.stats import gamma

#　事後分布のパラメータ 
a = 237; b = 20

# 分位点を求める関数, scaleは1/bする.
lower = gamma.ppf(0.025, a=a, scale=1/b)
upper = gamma.ppf(1-0.025, a=a, scale=1/b)
print([float(lower), float(upper)])
```
以下の結果を得る. 
```python
[10.389238190941795, 13.405448325642006]
```

$\theta_B \mid y_{B1}, \dots, y_{Bn} \sim gamma(125, 14)$ より, Pythonで以下のようにして求める.

```python
from scipy.stats import gamma

#　事後分布のパラメータ 
a = 237; b = 20

# 分位点を求める関数, scaleは1/bする.
lower = gamma.ppf(0.025, a=a, scale=1/b)
upper = gamma.ppf(1-0.025, a=a, scale=1/b)
print([float(lower), float(upper)])
```
以下の結果を得る. 
```python
[7.432064219464302, 10.560308149242363]
```

### (b)
問題文および (a)より

$$
\theta_B \sim gamma(12n_0 + 113,\, n_0 + 13)
$$

であり, 事後期待値は

$$
\begin{aligned}
E[\theta_B \mid y_{B1}, \dots, y_{Bn}] &= \frac{12n_0 + 113}{n_0 + 13}
\end{aligned}
$$

である. (a)の結果を用いると

$$
\begin{aligned}
E[\theta_A \mid y_{A1}, \dots, y_{Am}] =  E[\theta_B \mid y_{B1}, \dots, y_{Bn}] &\Leftrightarrow \frac{12n_0 + 113}{n_0 + 13} = \frac{237}{20}\\ 

 &\Leftrightarrow n_0 = \frac{821}{3} \approx 273.666\dots
\end{aligned}
$$

となる. $n_0 = 273$ および $n_0 = 274$ のとき

$$
\begin{aligned}
E[\theta_A \mid y_{A1}, \dots, y_{Am}] - E[\theta_B \mid y_{B1}, \dots, y_{Bn}] &= -0.00034965 \dots \\

E[\theta_A \mid y_{A1}, \dots, y_{Am}] - E[\theta_B \mid y_{B1}, \dots, y_{Bn}] &= 0.000174216

\end{aligned}
$$

となるので, $n_0 =274$ のときほぼ近い値を取る.

また, 以下のコードで, 事後期待値を可視化する.

```python
import numpy as np
import matplotlib.pyplot as plt

def gamma_mean(n_0):
    return (12 * n_0 + 113)/ (n_0 + 13)

n_0 = np.arange(1, 51)

plt.figure(figsize=(6, 4))
plt.plot(n_0, gamma_mean(n_0))
plt.xlabel("n_0")
plt.ylabel("posterior mean")
plt.title("Gamma Mean as a Function of n_0")
plt.grid(True)
plt.tight_layout()
plt.show()
```
出力結果は以下である.
![](https://static.zenn.studio/user-upload/6562cc0bc131-20260425.png)

### (c)
問題文より, 「B系統のマウスはA系統のマウスと関連性がある」と記載されているが[^引用252],  $p(\theta_A, \theta_B) = p(\theta_A) p(\theta_B)$ と仮定すると, 独立であることを意味してしまうので, 適切ではない. 


## 演習問題3.4
あらかじめ必要なライブラリや関数を作成しておく.
```python
import numpy as np

from scipy.stats import beta
from scipy.stats import binom

import matplotlib.pyplot as plt

plt.style.use("ggplot")

def subplots_distribution(ax, x, y, title):

    ax.plot(x, y)
    ax.set_xlabel(r"$\theta$")
    ax.set_ylabel("CDF")
    ax.set_title(title)
    ax.grid(True)
```

### (a)
$\theta$ の事前分布を $beta(2, 8)$ としたときの $p(\theta),\, p(y \mid \theta),\, p(\theta \mid y)$ を可視化するコードは以下を使用した.

```python
# パラメータ
a = 2
b = 8
n = 43
y = 15

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

x = np.linspace(0, 1, 200)
title = fr"$p(\theta)$ (a={a}, b={b})"
cdf = beta.pdf(x, a, b)
subplots_distribution(axes[0], x, cdf, title)

p = np.linspace(0, 1, 500)
cdf = binom.pmf(y, n, p)
title = fr"$p(y \mid \theta)$ (n = {n}, y={y})" 
subplots_distribution(axes[1], p, cdf, title)

title = fr"$p(\theta \mid y)$ (a = {a}, b = {b}, n = {n}, y = {y})"
cdf = beta.pdf(x, y + a, n - y + b)
subplots_distribution(axes[2], x, cdf, title)

plt.tight_layout()
plt.show()
```
出力結果は以下である.
![](https://static.zenn.studio/user-upload/0edd7609a9ad-20260425.png)

統計量は以下のコードで作成した.
```python
E_post = (a + y) / (a + b + n)
mode_post = (a + y - 1) / (a + b + n - 2)
std_var= np.sqrt((E_post * (1 - E_post)) / (a + b + n + 1))

print("事後期待値 : ", E_post)
print("事後モード : ", mode_post)
print("事後標準偏差 : ", float(std_var))
print("95%ベイズ信用区間：", beta.ppf(np.array([0.025, 0.975]), a + y, b + n - y))
```
結果は以下である.
```python
事後期待値 :  0.32075471698113206
事後モード :  0.3137254901960784
事後標準偏差 :  0.0635188989834093
95%ベイズ信用区間： [0.20329779 0.45102398]
```

### (b)
事前分布を $beta(8, 2)$ とした場合の結果は以下のコードで可視化した.
```python
# パラメータ
a = 8
b = 2
n = 43
y = 15

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

x = np.linspace(0, 1, 200)
title = fr"$p(\theta)$ (a={a}, b={b})"
cdf = beta.pdf(x, a, b)
subplots_distribution(axes[0], x, cdf, title)

p = np.linspace(0, 1, 500)
cdf = binom.pmf(y, n, p)
title = fr"$p(y \mid \theta)$ (n = {n}, y={y})" 
subplots_distribution(axes[1], p, cdf, title)

title = fr"$p(\theta \mid y)$ (a = {a}, b = {b}, n = {n}, y = {y})"
cdf = beta.pdf(x, y + a, n - y + b)
subplots_distribution(axes[2], x, cdf, title)

plt.tight_layout()
plt.show()
```
![](https://static.zenn.studio/user-upload/669241fff9c2-20260425.png)


統計量は以下で算出した.

```python
E_post = (a + y) / (a + b + n)
mode_post = (a + y - 1) / (a + b + n - 2)
std_var= np.sqrt((E_post * (1 - E_post)) / (a + b + n + 1))

print("事後期待値 : ", E_post)
print("事後モード : ", mode_post)
print("事後標準偏差 : ", float(std_var))
print("95%ベイズ信用区間：", beta.ppf(np.array([0.025, 0.975]), a + y, b + n - y))
```

```python
事後期待値 :  0.4339622641509434
事後モード :  0.43137254901960786
事後標準偏差 :  0.06744531631926798
95%ベイズ信用区間： [0.30469562 0.5679528 ]
```

### (c)

```python
x = np.linspace(0, 1, 200)

a = 2; b = 8
title_2_8 = fr"$p(\theta)$ (a={a}, b={b})"
cdf_2_8 = beta.pdf(x, a, b)

a = 8; b = 2
title_8_2 = fr"$p(\theta)$ (a={a}, b={b})"
cdf_8_2= beta.pdf(x, a, b)

title = fr"$p(\theta)$, mixture beta(2, 8) and beta(8, 2)"

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

subplots_distribution(axes[0], x, cdf_2_8, title_2_8)
subplots_distribution(axes[1], x, cdf_8_2, title_8_2)

title = r"$p(\theta)$, mixture beta(2, 8) and beta(8, 2)"
subplots_distribution(axes[2], x, cdf_2_8 * 0.75 + cdf_8_2 * 0.25, title)

plt.tight_layout()
plt.savefig("3-4-c.png")
plt.show()
```
![](https://static.zenn.studio/user-upload/4df56682a1a2-20260425.png)


### (d)
ガンマ関数を用いると

$$
\begin{aligned}
p(y \mid \theta) 

 &= \binom{n}{y} \theta^y (1 - \theta)^{n - y} \\

 &= \frac{n!}{y! (n - y)!} \theta^y (1 - \theta)^{n - y} \\

 &= \frac{\Gamma(n + 1)}{\Gamma(y + 1) \Gamma(n - y + 1)}\, \theta^y (1 - \theta)^{n - y}
\end{aligned}
$$

であることに注意する.
#### (1)
$n = 43, y = 15$ とすると上式より

$$
\begin{aligned}
p(\theta) p(y \mid \theta) 

 &= \frac{1}{4}\frac{\Gamma(10)}{\Gamma(2) \Gamma(8)} \left(3\theta (1 - \theta)^7 + \theta^7 (1 - \theta) \right)* \frac{\Gamma(44)}{\Gamma(16) \Gamma(29)}\, \theta^{15} (1 - \theta)^{28} \\

 &= \frac{1}{4} \frac{\Gamma(10) \Gamma(44)}{\Gamma(2)\Gamma(8)\Gamma(16) \Gamma(29)} \left(3 (1 - \theta)^6 + \theta^6 \right)\, \theta^{16} (1 - \theta)^{29}
\end{aligned}
$$

と表される.

#### (2)
(1)の結果をもう少し変形する.

$$
\begin{aligned}
p(\theta) p(y \mid \theta) 

 &\propto \frac{3}{4} \theta^{16} (1 - \theta)^{35} + \frac{1}{4}\theta^{22} (1 - \theta)^{29}
\end{aligned}
$$

となる. これは, $beta(17, 36)$ および $beta(23, 29)$ の混合分布である.


#### (3)
(1)で求めた $p(\theta) p(y \mid \theta)$ を可視化する. 使用したコードは以下である. なお, ガンマ関数部分は, 階乗が含まれており, 数値計算上の問題から, 先に `gammaln` を使って計算することで対応した.

```python
from scipy.special import gammaln

# θの範囲
theta = np.linspace(0, 1, 500)

# 定数部分（logで計算）
log_const = (
    np.log(1/4)
    + gammaln(10) - gammaln(2) - gammaln(8)
    + gammaln(44) - gammaln(16) - gammaln(29)
)

const = np.exp(log_const)

# 関数本体
y = const * (
    3 * theta**16 * (1 - theta)**35
    + theta**22 * (1 - theta)**29
)

# プロット
plt.figure()
plt.plot(theta, y)
plt.xlabel(r"$\theta$")
plt.ylabel(r"$p(\theta)\, p(y \mid \theta)$")
plt.title(r"$p(\theta)\, p(y \mid \theta)$")
plt.grid(True)
plt.show()
```
以下の結果を得た. 
![](https://static.zenn.studio/user-upload/044d3be34d01-20260425.png)

事後モードは

$$
\begin{aligned}
\theta_{mode} 

 &= \arg\max_{\theta}\, p(\theta \mid y) \\
 
 &= \arg\max_{\theta}\, p(y \mid \theta)\, p(\theta) \\

 &= \arg\max_{\theta}\, \frac{3}{4} \theta^{16} (1 - \theta)^{35} + \frac{1}{4}\theta^{22} (1 - \theta)^{29}

\end{aligned}
$$

により求められる. 以下のコードを用いて, 数値的に求める.

```python
from scipy.optimize import minimize_scalar

# 事後分布の数式
def f(theta):
    return (3/4)*theta**16*(1-theta)**35 + (1/4)*theta**22*(1-theta)**29

# 符号を反転して最小化
res = minimize_scalar(lambda x: -f(x), bounds=(0, 1), method='bounded')

theta_max = res.x
f_max = f(theta_max)

print("最頻値：", theta_max)
```
出力結果は以下である. 
```python
最頻値： 0.3140865716397876
```
(a), (b)の事後モードと並べると次のようになる. (a)のときの値に近い.

| 項目 | 値 |
|------|---------------------|
| (a)の事後モード  | 0.3137254901960784  |
| (b)の事後モード  | 0.43137254901960786 |
| (d)の事後モード  | 0.3140865716397876  |


### (e)
混合分布の重みを一般化する. つまり, (d)において, $w \in [0, 1]$ として

$$
p(\theta) = \frac{\Gamma(10)}{\Gamma(2)\Gamma(8)}(w\, \theta(1- \theta)^7 + (1 - w)\, \theta^7 (1- \theta))
$$

を考える. このとき


$$
\begin{aligned}
p(\theta \mid y) &\propto p(y \mid \theta)\, p(\theta) \\
&=\frac{\Gamma(n + 1)}{\Gamma(y + 1) \Gamma(n - y + 1)}\, \theta^y (1 - \theta)^{n - y} \\

&\quad \times \frac{\Gamma(10)}{\Gamma(2)\Gamma(8)}(w\, \theta(1- \theta)^7 + (1 - w)\, \theta^7 (1- \theta)) \\

&\propto w\, \theta^{y + 1}(1 - \theta)^{n - y + 7} + (1 - w)\, \theta^{y + 7} (1 - \theta)^{n - y + 1} 
\end{aligned}
$$

である. また, 事後分布の定数部分を求める.

$$
\begin{aligned}
\int_0^1 w\, \theta^{y + 1}(1 - \theta)^{n - y + 7}  d\theta &= w B(y+2, n-y+8) \\
 &= w\,\frac{\Gamma(y + 2)\Gamma(n - y + 8)}{\Gamma(n + 10)} \\

\int_0^1  (1 - w)\, \theta^{y + 7} (1 - \theta)^{n - y + 1} d\theta &= (1- w)B(y + 8, n - y + 2) \\

&= (1 - w)\, \frac{\Gamma(y + 8)\Gamma(n - y + 2)}{\Gamma(n + 10)}
 
\end{aligned}
$$

を用いることで

$$
p(\theta \mid y) = \frac{w\, \theta^{y + 1}(1 - \theta)^{n - y + 7} + (1 - w)\, \theta^{y + 7} (1 - \theta)^{n - y + 1} }{\displaystyle w\,\frac{\Gamma(y + 2)\Gamma(n - y + 8)}{\Gamma(n + 10)} + (1 - w)\, \frac{\Gamma(y + 8)\Gamma(n - y + 2)}{\Gamma(n + 10)}}
$$

を得る.[^解釈]

## 演習問題3.5
### (a)
3.3節にあるように パラメータ $\phi$ の共役事前分布 を

$$
p(\phi \mid n_k, t_k) = \kappa(n_k, t_k) c(\phi)^{n_k} e^{n_k t_k \phi}
$$

とおくと

$$
\begin{aligned}
p(\theta \mid y_1, \cdots, y_n) &\propto p(y_1, \cdots, y_n \mid \theta) p(\theta) \\
    &= \prod_{i = 1}^n \left(c(\theta) h(y_i) \exp(\theta t(y_i)) \right) \cdot \sum_{k = 1}^K w_k p_k(\theta) \\
    &\propto c(\theta)^n \exp \left(\theta \sum_{i = 1}^n t(y_i) \right) \left( \sum_{k = 1}^K w_k p_k(\theta) \right) \\
    &= c(\theta)^n \exp \left(\theta \sum_{i = 1}^n t(y_i) \right) \left( \sum_{k = 1}^K w_k \kappa_k(n_k, t_k) c(\theta)^{n_k} e^{n_k t_k \theta} \right) \\
    &= \sum_{k = 1}^K w_k \kappa_k(n_k, t_k) c(\theta)^{n + n_k} \exp \left( \left(n_k t_k + \sum_{i = 1}^n t(y_i) \right) \theta \right) 
\end{aligned}
$$

を得る.


## 演習問題3.10
### (a)
$\theta \sim \operatorname{beta}(a, b)$ とし, $\displaystyle \psi = \log \left(\frac{\theta}{1 - \theta} \right)$ とすると

$$
h(\psi) = \frac{e^{\psi}}{1 + e^{\psi}}
$$

であるので

$$
\begin{aligned}
p_\psi(\psi) 
    &= p_\theta (h(\psi)) \cdot \left|\frac{d h(\psi)}{d \psi} \right| \\
    &= \frac{\Gamma(a + b)}{\Gamma(a) \Gamma(b)} \left(\frac{e^\psi}{1 + e^\psi} \right)^{a - 1}\left(\frac{1}{1 + e^\psi} \right)^{b - 1} \cdot \frac{e^\psi}{(1 + e^\psi)^2} \\
    &= \frac{\Gamma(a + b)}{\Gamma(a) \Gamma(b)} \left(\frac{e^\psi}{1 + e^\psi} \right)^{a} \left(\frac{1}{1 + e^\psi} \right)^{b}
\end{aligned}
$$

となる. 

```python
import numpy as np
import matplotlib.pyplot as plt

plt.style.use("ggplot")

def prob_psi_a(psi):
    return np.exp(psi) / (1 + np.exp(psi))**2

# 乱数を発生させる. a = b = 1の時は一様分布になる.
theta_list = np.random.uniform(0, 1, size=1000)
psi_list = np.log(theta_list / (1 - theta_list))

plt.scatter(
    psi_list,
    prob_psi_a(psi_list)
)

plt.savefig("3-10-a.png")
plt.show()
```
結果は以下である.
![](/images/section-3/3-10-a.png)




### (b)
$\theta \sim \operatorname{gamma}(a, b)$ とし, $\psi = \log \theta$ とすると, (a) と同様にして

$$
p_\psi(\psi) = \frac{b^a}{\Gamma(a)} (e^\psi)^{a - 1} \cdot e^{- b e^\psi} \cdot e^\psi = \frac{b^a}{\Gamma(a)} e^{a\psi} \cdot e^{- b e^\psi} 
$$

を得る.


```python
def prob_psi_b(psi_list):
    return np.exp(-psi_list)

a = 1
b = 1
theta_list = np.random.gamma(shape=a, scale=1/b, size=1000)
psi_list = np.log(theta_list)

plt.scatter(
    psi_list,
    prob_psi_b(psi_list)
)
plt.savefig("3-10-b.png")
plt.show()
```

![](/images/section-3/3-10-b.png)


[^解釈]: 解釈まではよくわからなかった. 誰か詳しい人がいたらコメントが欲しいです.
[^引用252]: p252の3.3の問題文