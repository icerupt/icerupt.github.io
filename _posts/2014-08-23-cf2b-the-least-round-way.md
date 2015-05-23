---
layout: post
title: "[cf2b] The least round way"
description: ""
category: "codeforces"
tags: ["动态规划", "数学", "codeforces"]
---
{% include JB/setup %}

### 题意
给定一个 \\(n \times n\\) 的非负整数矩阵，要从左上角走到右下角，只能
向右向下走。将经过的数字相乘，要使最后结果的尾零最少。输出尾零个数
并输出一种路径。

### 题解
尾零是由于因子 2 和 5 造成的，所以只需要关注每个数因子 2 和 5 的个数。
又由于决定尾零个数的是由因子 2 和 5 较小的那个因子数决定的。
不妨记 \\(f_i\\) 是某条路径 \\(i\\) 中 2 的个数, \\(g_i\\) 是某条
路径 \\(i\\) 中 5 的个数，容易分析出最后的结果为
$$ \mathrm{Ans} = \min \left\{ \min \left\{ f_i \right\},
		\min \left\{ g_i \right\} \right\} \nonumber $$

以上的分析有一个问题，由于是非负整数，所以 0 本身也能产生尾零，所以要对
路径中有零的情况单独处理。

最后输出记录的路径即可。

### 实现

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 1001;
const int INF = ~(1 << 31);
int f[N][N], g[N][N], a[N][N];
char tf[N][N], tg[N][N];

int dis(int x, int y)
{
	int t = 0;
	while (!(x%y) && x) {
		++t;
		x /= y;
	}
	return t;
}

void print(int x, int y, char (*t)[N])
{
	if (x || y) {
		if (t[x][y] == 'R') print(x, y-1, t);
		if (t[x][y] == 'D') print(x-1, y, t);
		cout << t[x][y];
	}
}

int main()
{

	fill(f[0], f[0] + N*N, INF);
	fill(g[0], g[0] + N*N, INF);
	int n, px;
	cin >> n;
	bool zero = false;
	for (int i = 0; i != n; ++i)
		for (int j = 0; j != n; ++j) {
			cin >> a[i][j];
			if (!a[i][j]) {
				zero = true;
				px = i;
			}
		}

	f[0][0] = dis(a[0][0], 2); g[0][0] = dis(a[0][0], 5);
	for (int i = 0; i != n; ++i)
		for (int j = 0; j != n; ++j) {
			if (i != 0) {
				f[i][j] = f[i - 1][j];
				tf[i][j] = 'D';
				g[i][j] = g[i - 1][j];
				tg[i][j] = 'D';
			}
			if (j != 0) {
				if (f[i][j - 1] < f[i][j]) {
					f[i][j] = f[i][j - 1];
					tf[i][j] = 'R';
				}
				if (g[i][j - 1] < g[i][j]) {
					g[i][j] = g[i][j - 1];
					tg[i][j] = 'R';
				}
			}
			if (!i && !j) continue;
			f[i][j] += dis(a[i][j], 2); g[i][j] += dis(a[i][j], 5);
		}
	if (min(f[n-1][n-1], g[n-1][n-1]) > 1 && zero) {
		cout << 1 << endl;
		for (int i = 0; i != px; ++i) cout << 'D';
		for (int i = 1; i != n; ++i) cout << 'R';
		for (int i = px + 1; i != n; ++i) cout << 'D';
	} else
	if (f[n - 1][n - 1] < g[n - 1][n - 1]) {
		cout << f[n - 1][n - 1] << endl;
		print(n - 1, n - 1, tf);
	} else {
		cout << g[n - 1][n - 1] << endl;
		print(n - 1, n - 1, tg);
	}
}

```

