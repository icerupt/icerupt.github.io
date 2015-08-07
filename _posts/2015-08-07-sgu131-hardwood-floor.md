---
layout: post
title: "[sgu131] Hardwood floor"
description: ""
category: "sgu"
tags: ["状态压缩动态规划", "覆盖"]
---
{% include JB/setup %}

{% raw %}

### 题意

问在一个 \\(M \times N (1 \leq M \leq 9, 1 \leq N \leq) \\) 的地板上用 rectangles(\\(2 \times 1\\) 的方块) 以及 corners(\\(2 \times 2\\) 的方块去掉一个 \\(1 \times 1\\) 的方块)，
这两种木板铺满，有多少种方案。

### 题解

典型的状态压缩动态规划，因为有了 corners 稍微麻烦点，做法还是一样。
用 \\(f\_{i, prow}\\) 表示前 \\(i-2\\) 行已经铺满，前一行状态为 prow，
然后dfs转移到当前行就行。转移的细节可以参考代码注释。

为了方便可以在第一行前设置一行哨兵行，都已经铺满，方案数为 \\(1\\)。
这题要滚动数组。

### 实现

```cpp

#include <iostream>

#define __DEBUG
#undef __DEBUG

int const maxn = 10;
long long f[2][1 << maxn];
int n, m, len, current;

void init_f(int c)
{
	for (int i = 0; i < len; i++) f[c][i] = 0;
}

void dfs(int col, int pprow, int prow, int row) // state for previous row and state for current row
{
	if (col == m) f[current][row] += f[current ^ 1][pprow];

#ifdef __DEBUG
	if (col == m)
		std::cout << prow << ' ' << row << '\n';
#endif

	if (col >= m) return;

	// ep: is empty previous row  ec: is empty current row
	int ep0 = prow & (1<<col), ep1 = prow & (1<<(col+1));
	int ec0 =  row & (1<<col), ec1 =  row & (1<<(col+1));

	// put rectangle vertically
	if (!ep0 && !ec0)
		dfs(col + 1, pprow, prow | (1<<col), row | (1<<col));

	// left empty
	if (ep0)
		dfs(col + 1, pprow, prow, row);

	// put rectangle horizontally
	if (col < m-1 && ep0 && !ec0 && !ec1)
		dfs(col + 1, pprow, prow, row | (1<<col) | (1<<(col+1)));


	// put like this 	*
	//					**
	if (col < m-1 && !ep0 && !ec0 && !ec1)
		dfs(col + 1, pprow, prow | (1<<col), row | (1<<col) | (1<<(col+1)));

	// put like this 	**
	//					*
	if (col < m-1 && !ep0 && !ep1 && !ec0)
		dfs(col + 1, pprow, prow | (1<<col) | (1<<(col+1)), row | (1<<col));

	// put like this 	**
	//					 *
	if (!ep0 && !ep1 && !ec1)
		dfs(col + 2, pprow, prow | (1<<col) | (1<<(col+1)), row | (1<<(col+1)));

	// put like this 	 *
	//					**
	if (ep0 && !ep1 && !ec0 && !ec1)
		dfs(col + 2, pprow, prow | (1<<col), row | (1<<col) | (1<<(col+1)));
}

int main()
{
	std::cin >> n >> m;
	len = 1 << m;

	init_f(current); f[current][len - 1] = 1;
	for (int i = 1; i <= n; i++) {
		current ^= 1; init_f(current);
		for (int prow = 0; prow < len; prow++) {
			if (!f[current ^ 1][prow]) continue;
			dfs(0, prow, prow, 0);
		}
	}
	std::cout << f[current][len - 1] << '\n';
}

```

{% endraw %}

