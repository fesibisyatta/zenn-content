---
title: "標準 ベイズ統計学 第10章 演習問題 解答例"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["メトロポリスヘイスティングス", "反射型ランダムウォーク"]
published: false
---

## 演習問題10.1
$\tilde{\theta} \sim \operatorname{uniform}(\theta_0 - \delta, \theta_0 + \delta)$ の確率密度関数は

$$
f_{\tilde{\theta}}(t \mid \theta_0) = \frac{1}{2\delta} \mathbb{1}_{[\theta_0 - \delta, \theta_0 + \delta]}(t)
$$

である. $\theta_0$ が与えられた条件下での $\theta_1$ の密度関数を求める. $\theta_1 = |\tilde{\theta}|$ なので, 分布関数は

$$
\begin{aligned}
P(\theta_1 \leq y \mid \theta_0 ) 
    &= P(| \tilde{\theta} | \leq y \mid \theta_0 ) \\
    &= P(-y \leq \tilde{\theta}  \leq y \mid \theta_0 ) \\
    &= F_{\tilde{\theta}}(y \mid \theta_0) - F_{\tilde{\theta}}(-y \mid \theta_0) \\
\end{aligned}
$$

である. 両辺を微分することで, 密度関数 $J(\theta_1 \mid \theta_0)$ は

$$
\begin{aligned}
J(\theta_1 \mid \theta_0) 

    &= f_{\tilde{\theta}}(\theta_1 \mid \theta_0) + f_{\tilde{\theta}}(-\theta_1 \mid \theta_0) \\
    &= \frac{1}{2\delta} \mathbb{1}(|\theta_1 - \theta_0| \leq \delta) + \frac{1}{2\delta} \mathbb{1}(\theta_1 + \theta_0 \leq \delta)

\end{aligned}
$$

となる. この式から, $J(\theta_0 \mid \theta_1) = J(\theta_1 \mid \theta_0)$ が成り立つ. また, $\theta_1 = |\tilde{\theta}|$ より正の値を持つこともわかる.

## 演習問題10.2
