---
layout: post
title: "[poj2455] Secret Milking Machine"
description: ""
category: "poj"
tags: ["二分", "usaco", "最大流", "有待深入研究"]
---
{% include JB/setup %}

### 题意
在一个无向有重边的图中，要找从节点\\(1\\)到节点\\(n\\)无重复经过同一条边的路径\\(T\\)条。
并求所有路径中的最大边的最小值。

### 题解
很简单，先二分答案，然后用网络流判断方案数。好久没写网络流了，照着[kuangbin][1]的模板写了一个sap，
结果1000ms过了，最近事挺多。不想管了，题目本身也就这样，网络流的优雅实现之后研究。

### 实现
```cpp
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

struct data { int x, y, c; };
int const maxn = 201;
int map[maxn][maxn];

int gap[maxn], dis[maxn], pre[maxn], cur[maxn];

vector<data> edge;
typedef vector<data>::iterator e_it;

int n, m, t;

int sap(int start, int end, int nodenum)
{
	memset(cur, 0, sizeof(cur));
	memset(dis, 0, sizeof(dis));
	memset(gap, 0, sizeof(gap));
	int u = pre[start]=start, maxflow = 0, aug = -1;
	gap[0] = nodenum;
	while (dis[start] < nodenum) {
loop:
		for (int v = cur[u]; v < nodenum; v++)
			if (map[u][v] && dis[u] == dis[v] + 1) {
				if (aug == -1 || aug > map[u][v]) aug = map[u][v];
				pre[v] = u;
				u = cur[u]=v;
				if (v == end) {
					maxflow += aug;
					for (u = pre[u]; v != start; v = u, u = pre[u]) {
						map[u][v] -= aug;
						map[v][u] += aug;
					}
					aug = -1;
				}
				goto loop;
			}
		int mindis = nodenum - 1;
		for (int v = 0; v < nodenum; v++)
			if (map[u][v] && mindis > dis[v]) {
				cur[u] = v;
				mindis = dis[v];
			}
		if ((--gap[dis[u]]) == 0) break;
		gap[dis[u] = mindis+1]++;
		u = pre[u];
	}
	return maxflow;
}

bool judge(int x)
{
	memset(map, 0, sizeof(map));

	for (e_it it = edge.begin(); it != edge.end(); ++it) {
		if ((*it).c > x) continue;
		map[(*it).x][(*it).y]++;
		map[(*it).y][(*it).x]++;
	}
	if (sap(0, n-1, n) >= t) return true;
	return false;
}

int main()
{
	ios::sync_with_stdio(false);
	cin >> n >> m >> t;

	edge.resize(m);
	int min_edge = 1<<30, max_edge = 0;
	for (int i = 0; i < m; i++) {
		cin >> edge[i].x >> edge[i].y >> edge[i].c;
		edge[i].x--;  edge[i].y--;
		min_edge = min(min_edge, edge[i].c);
		max_edge = max(max_edge, edge[i].c);
	}

	int l = min_edge, r = max_edge;
	while (l+1 < r) {
		int mid = (l+r)/2;
		if (judge(mid)) r = mid;
		else            l = mid+1;
	}
	if (judge(l)) r = l;
	cout << r << "\n";
}

```
[1]: http://www.kuangbin.net/
