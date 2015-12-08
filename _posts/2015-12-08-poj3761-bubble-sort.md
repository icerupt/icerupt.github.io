---
layout: post
title: "[poj3761] Bubble Sort"
description: ""
category: "poj"
tags: ["数学", "找规律", "组合数学"]
---
{% include JB/setup %}
{% raw %}

### 题意

给定长度为 \\(n (\leq 10^6)\\) 的互不相同的有序的序列。
问有多少不同的初始序列，使得恰好经过 \\(k (0 \leq k \leq n-1)\\) 轮
Bubble Sort交换，使得序列有序（升序）。一轮Bubble Sort交换就是在Bubble Sort
算法中，一次线性扫描，交换相邻逆序对。

### 题解

一道不错的数学题。我们定义 \\(G(i)\\) 表示第 \\(i\\) 位数字左边比它大的数字的个数，
很明显，在一轮交换中，如果某一位 \\(G(i)\\) 不为零，\\(G(i)\\) 肯定会减一，
那么对于一个给定的序列我们需要的Bubble Sort的轮数就是 \\(max{G(i)}\\)。

假如我们将原始序列映射到 \\({1, 2, \dots, n}\\)，我们先求经过轮数小于等于 \\(k\\) 次成为有序的序列个数，
定义为 \\(F(n, k))。我们看到对于一个数 \\(x\\)， 比它大的数的个数为 \\(n - x\\) 个。
如果 \\(n - x \leq k\\) 即 \\(x \geq n - k\\)，那么这些数怎么放都是可以的。
我们将数划分成两部分 \\([1, n - k)\\) 和 \\([n-k, n]\\) 两部分，
后面 \\(k+1\\) 个数先随意排列有 \\((k+1)!\\) 种情况，然后考虑前一部分数。
对于 \\(1\\) 只能插在使得 \\(G(i)={0, 1, 2, \dots, k}\\) 的 \\(k + 1\\) 个位置，
对于 \\(2\\) 也是，接下来的数也是，所以共有 \\((k + 1)^{n - k - 1}\\) 种情况，故：

$$ \begin{equation*} F(n, k)=(k+1)!(k+1)^{n-k-1} \end{equation*} $$

最终答案就是：

$$ \begin{align*}
	ans &= F(n, k)-F(n, k-1) \\\\
		&= (k+1)!(k+1)^{n-k-1} - k!k^{n-k} \\\\
\end{align*} $$

题目要求取模 \\(20100713\\)，这是一个素数，虽然并没有什么用。

### 实现

```cpp

// poj3761
// 统计：688ms, 1wa
#include <iostream>

int const mo = 20100713;
int const maxn = 1000007;

long long factor[maxn];

void init_factor()
{
	factor[0] = 1;
	for (int i = 1; i < maxn; i++) factor[i] = (factor[i - 1] * i) % mo;
}

long long quick(int a, int k)
{
	if (!k) return 1;
	long long tmp = quick(a, k/2);
	tmp = (tmp * tmp) % mo;
	if (k & 1) tmp = (tmp * a) % mo;
	return tmp;
}

long long calc(int n, int k)
{
	if (k == 0) return 1;
	long long ret = factor[k];
	long long tmp = quick(k + 1, n - k);
	ret = (ret * tmp) % mo;
	tmp = (factor[k - 1] * quick(k, n - k + 1)) % mo;
	ret = (ret - tmp + mo) % mo;
	return ret;
}

int main()
{
	init_factor();
	std::ios::sync_with_stdio(false);
	int T; std::cin >> T;
	while (T--) {
		int n, k;
		std::cin >> n >> k;
		std::cout << calc(n, k) << '\n';
	}
}

```

{% endraw %}

