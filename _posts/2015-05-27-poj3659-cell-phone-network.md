---
layout: post
title: "[poj3659] Cell Phone Network"
description: ""
category: "poj"
tags: ["动态规划", "usaco", "树形动态规划", "最小支配集"]
---
{% include JB/setup %}

{% raw %}

### 题意

给定一个图，求最小点支配集的大小。

### 题解

其实很明显是一个树形dp，用 \\(f\_{i, 1}\\) 表示以 \\(i\\) 为根且当前点取的最小个数，
\\(f\_{i, 0}\\) 表示当且点不取，且所有儿子结点中至少有一个取的最小个数，
\\(f\_{i, 2}\\) 表示当且点不取，且父亲结点取的最小个数，
然后就很容易想到递推了。要注意的是 \\(f\_{i, 0}\\)，要注意保证儿子节点中至少一个取的条件。

### 实现

```cpp
#include <iostream>
#include <vector>

int const maxn = 10004;
int const inf = 1<<20;
bool vis[maxn];
int f[maxn][3];
int n;

typedef std::vector<std::vector<int> > graph_type;
typedef std::vector<int>::const_iterator e_cit;
graph_type graph;

void dfs(int root)
{
	if (f[root][0] != -1) return;
	vis[root] = true;
	f[root][0] = f[root][2] = 0;
	f[root][1] = 1;
	int min_delta = inf, child = 0;
	bool all_child_0 = true;
	for (e_cit it = graph[root].begin(); it != graph[root].end(); ++it) {
		int u = *it;
		if (vis[u]) continue;
		dfs(u);
		child++;
		f[root][1] += std::min(std::min(f[u][0], f[u][2]), f[u][1]);
		f[root][2] += std::min(f[u][0], f[u][1]);
		f[root][0] += std::min(f[u][0], f[u][1]);
		if (f[u][1] > f[u][0]) {
			if (f[u][1] - f[u][0] < min_delta) {
				min_delta = f[u][1] - f[u][0];
			}
		} else {
			all_child_0 = false;
		}
	}
	if (all_child_0 || !child ) f[root][0] += min_delta;
}

int main()
{
	std::cin >> n;
	graph.resize(n);
	for (int i = 0; i < n; i++) f[i][0] = f[i][1] = -1;
	for (int i = 0, x, y; i < n-1; i++) {
		std::cin >> x >> y;
		x--; y--;
		graph[x].push_back(y);
		graph[y].push_back(x);
	}
	dfs(0);
	std::cout << std::min(f[0][0], f[0][1]) << "\n";
}

```

{% endraw %}
