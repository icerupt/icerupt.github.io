---
layout: post
title: "\[sgu121\] Bridges painting"
description: ""
category: "sgu"
tags: ["图论", "构造", "欧拉回路"，"细节"]
---
{% include JB/setup %}

{% raw %}

### 题意

给定一个 \\(n\\) 个点的无向简单图，要将每条边染成黑色或者白色，
要使每个有至少两条边与之相连的节点，至少有一条白边和一条黑边。

### 题解

初步的想法，用 euler tour 来构造，假如说给一条欧拉（回）路上的边交替染黑色和白色，
那么中间经过一个点肯定至少有条白边以及黑边。所以感觉欧拉路的做法是挺正确的。

现在来考虑一些细节以及无解的情况。

对于每个连通块，如果只有一对奇度点，那么正好对应一条欧拉路，所以这个连通块肯定是有解的。

对于每个连通块，如果没有奇度点，那么正好对应条欧拉回路，如果回路的长度为偶数那么依旧有解，
如果回路长度为奇数那么就无解。

对于每个连通块，如果有大于一对奇度点，那么我们可以考虑这样的方法，
每次从一个奇度点开始到另一个奇度点结束，去掉一条欧拉路径，这一条欧拉路径的染色是可行的，
并且这个连通块将会少掉一对奇度点（当然同时有可能变成多个连通块）。然后如此反复肯定会只剩只有偶度点。

我们先处理奇度点，剩下若干联通分块都只有偶度点，不停的跑欧拉回路就行，不过还有一个要注意的地方，
此时如果一个联通分块的欧拉回路的长度是奇数不一定就无解，因为可以把起始点选在度大于 \\(2\\) 的位置，
之前这个点被其他的欧拉路经过，所以是符合的。


实现也是比较坑爹。每次去一条欧拉路这个可以参考代码，只要找到第一个不能再拓展的点，
就把 dfs 栈都弹掉。为了实现方便，最后再 check 一下来判断是否无解。这道题数据范围比较小，
直接临接表(vector)就行，数据量大点的例子可以参考这道题 hdu5348, [传送门][1]。

### 实现

```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <deque>

int const maxn = 102;
bool vis[maxn];
bool vis_edge[maxn][maxn];
int indegree[maxn];
int color[maxn][maxn];
int n;

typedef std::vector<int> edge_type;
typedef edge_type::iterator edge_it;
std::vector<edge_type> graph;

int dfs(int x)
{
	vis[x] = true;
	int sum = (indegree[x]&1);
	for (edge_it it = graph[x].begin(); it != graph[x].end(); ++it) {
		int v = *it;
		if (vis[v]) continue;
		sum += dfs(v);
	}
	return sum;
}

bool eulerian_tour_odd_path(int x, int co) // 1: white  2: black
{
	for (edge_it it = graph[x].begin(); it != graph[x].end(); ++it) {
		int v = *it;
		if (vis_edge[x][v]) continue;
		vis_edge[x][v] = vis_edge[v][x] = true;
		color[x][v] = color[v][x] = co;
		indegree[x]--; indegree[v]--;
		if (eulerian_tour_odd_path(v, 3 - co)) return true;
	}
	return true;
}

std::deque<int> euler_tour;
void eulerian_tour(int x)
{
	for (edge_it it = graph[x].begin(); it != graph[x].end(); ++it) {
		int v = *it;
		if (vis_edge[x][v]) continue;
		vis_edge[x][v] = vis_edge[v][x] = true;
		indegree[x]--; indegree[v]--;
		eulerian_tour(v);
	}
	euler_tour.push_back(x);
}

void color_euler_tour()
{
	for (int i = 1, co = 1; i < (int)euler_tour.size(); i++) {
		int u = euler_tour[i - 1], v = euler_tour[i];
		color[u][v] = color[v][u] = co;
		co = 3 - co;
	}
}

int main()
{
	std::cin >> n;
	graph.resize(n);
	for (int i = 0, x; i < n; i++)
		while (std::cin >> x && x) {
			graph[i].push_back(--x);
			indegree[x]++;
		}

	for (int i = 0; i < n; i++)
		if (indegree[i] & 1) {
			std::fill(vis, vis + n, 0);
			int odd_degree = dfs(i);
			if (odd_degree == 2) {
				euler_tour.clear();
				eulerian_tour(i);
				color_euler_tour();
			} else {
				eulerian_tour_odd_path(i, 1);
			}
		}

	for (int i = 0; i < n; i++)
		if (indegree[i]) {
			euler_tour.clear();
			eulerian_tour(i);

			for (int count = euler_tour.size(); count
					&& graph[euler_tour[0]].size() <= 2; count--) {
				euler_tour.pop_front();
				euler_tour.push_back(euler_tour[0]);
			}
			color_euler_tour();
		}


	// No solution
	for (int i = 0; i < n; i++) {
		bool black = false, white = false;
		if (graph[i].size() <= 1) continue;
		for (edge_type::iterator it = graph[i].begin(); it != graph[i].end(); ++it) {
			int v = *it;
			if (color[i][v] == 1) white = true;
			else
			if (color[i][v] == 2) black = true;
		}
		if (!(white && black)) {
			std::cout << "No solution\n";
			return 0;
		}
	}

	for (int i = 0; i < n; i++) {
		for (edge_type::iterator it = graph[i].begin(); it != graph[i].end(); ++it) {
			int v = *it;
			std::cout << color[i][v] << ' ';
		}
		std::cout << "0\n";
	}
}

```
[1]: http://acm.hdu.edu.cn/showproblem.php?pid=5348

{% endraw %}

