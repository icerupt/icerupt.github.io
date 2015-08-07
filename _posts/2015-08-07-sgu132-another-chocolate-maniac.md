---
layout: post
title: "[sgu132] Another Chocolate Maniac"
description: ""
category: "sgu"
tags: ["状态压缩动态规划", "覆盖"]
---
{% include JB/setup %}

{% raw %}

### 题意

给定一个 \\(M \times N (1 \leq M \leq 70, 1 \leq N \leq 7)\\) 的矩形蛋糕，上面有些格有蜡烛，用 '\*' 表示，
其余表示空着。现在有很多 \\(1 \times 2\\) 的巧克力，
问在保证蛋糕上没有连续两块相邻的空格的条件下，最少放多少块巧克力。

### 题解

一开始往二分图覆盖的方向想，无果...后来发现既然 \\(N\\) 这么小，
可以状态压缩dp搞。

还是用二进制压缩一行的状态，\\(0\\) 表示空着，\\(1\\) 表示放了巧克力或者不能放。
因为状态和上一行有关，我们设计这样的状态，\\(f\_{i, row, prow}\\) 表示前 \\(i-2\\) 行都满足要求，
当前行的状态为 \\(row\\)，前一行的状态为 \\(prow\\) 最小的巧克力数目。
然后转移的时候枚举当前行以及前一行的状态，搜索出下一行的状态，进行转移。

转移的实现可以参考代码，注释也比较清楚了。大概就是放到当前行某一列，可以考虑和前一行竖着放，
和后一行竖着放，在当前行横着放，留空这么四种情况。

实现的时候要用滚动数组。

### 实现

```cpp

#include <iostream>

short const inf = 1<<13;
short const maxn = 77;
short const maxm = 8;
short f[2][1<<maxm][1<<maxm];
short pow2[maxm];
short da[maxn];
short n, m, len;
short current;

void init_f(int now)
{
	for (int i = 0; i < len; i++)
		for (int j = 0; j < len; j++) f[now][i][j] = inf;
}

void init_pow2()
{
	pow2[0] = 1;
	for (int i = 1; i < maxm; i++) pow2[i] = pow2[i-1] * 2;
}

void dfs(short col, short prow, short row, short now, short next, short add)
{
	if (col == m)
		f[current][now][next] = std::min(f[current][now][next],
				short(f[current ^ 1][prow][row] + add));
	if (col >= m) return;

	int e0 = prow & pow2[col];   // check empty prow at current col
	int e1 = now &  pow2[col];   // check empty  now at current col
	int e2 = next & pow2[col];   // check empty next at current col
	int en1 = now & pow2[col+1]; // check empty now at next col
	int en0 = 1;
	if (col) en0 = now & pow2[col-1]; // check empty now at prev col

	// put vertically at prow && now
	if (!e0 && !e1)
		dfs(col + 1, prow, row, now ^ pow2[col], next, add + 1);

	// put vertically at now && next
	if (!e1 && !e2)
		dfs(col + 1, prow, row, now ^ pow2[col], next ^ pow2[col], add + 1);

	// put sideway at now
	if (!e1 && !en1)
		dfs(col + 2, prow, row, now ^ pow2[col] ^ pow2[col+1], next, add + 1);

	// left empty
	if ((e1 || en0) && !(!e1 && !e0))
		dfs(col + 1, prow, row, now, next, add);
}

int main()
{
	init_pow2();
	std::cin >> n >> m;
	len = 1<<m;
	da[0] = da[n+1] = len-1;
	for (int i = 1; i <= n; i++) {
		int tmp = 0;
		for (int j = 0; j < m; j++) {
			char ch; std::cin >> ch;
			tmp = tmp * 2 + (ch == '*');
		}
		da[i] = tmp;
	}

	current = 0; init_f(current);
	f[current][da[0]][da[1]] = 0;
	for (int i = 1; i <= n; i++) {
		current ^= 1; init_f(current);
		for (int prow = 0; prow < len; prow++) {
			if ((prow & da[i-1]) != da[i-1]) continue;
			for (int row = 0; row < len; row++) {
				if ((row & da[i]) != da[i]) continue;
				if (f[current ^ 1][prow][row] >= inf) continue;
				dfs(0, prow, row, row, da[i+1], 0);
			}
		}
	}

	short ans = inf;
	for (int i = 0; i < len; i++)
		ans = std::min(ans, f[current][i][len-1]);

	std::cout << ans << '\n';
}

```

{% endraw %}

