---
layout: post
title: "[poj3762] The Bonus Salary!"
description: ""
category: "poj"
tags: ["费用流"]
---
{% include JB/setup %}
{% raw %}

### 题意

给定 \\(n(\leq 2000)\\) 项工作的起始时间和结束时间（一天24小时内）以及相应的生成率分数，
每一天可以选择若干项时间不冲突（重叠）的工作完成，获得相应的分数。
每项任务只能被做一次，现在你有 \\(k(\leq 100)\\) 天时间，问最多可以获得多少分数？

### 题解

不要被poj的数据范围吓到。费用流可以过。先将所有时间离散化按先后顺序分别标为 \\(1, 2, 3 \dots\\)，
然后将第 \\(i\\) 个时间点连一条容量为 \\(k\\)，费用为 \\(0\\) 的边到 \\(i+1\\)，
源点连条容量为 \\(k\\)，费用为 \\(0\\) 的边到 \\(1\\) 号点，
最后一个点也连条容量费用同样的边到汇点。这个表示的是时间节点按序发生，
然后对于第 \\(i\\) 个事件，假设离散化时候的时间区间为 \\([u, v]\\)，
那么连一条容量为 \\(1\\)，费用为负分数的边从 \\(u\\) 到 \\(v\\)。这样就可以保证不想交了。

跑一遍最小费用最大流，负的结果就是答案。

### 实现

```cpp

// run: $exec < input
#include <iostream>
#include <algorithm>
#include <map>
#include <set>
#include <vector>
#include <queue>
#include <utility>

namespace mcmf
{
	typedef std::pair<int, int> P;
	struct edge {int to, cap, cost, rev; };

	int const MAX_V = 5008;
	int const INF = 1<<28;
	int V;
	std::vector<edge> G[MAX_V];
	int h[MAX_V];
	int dist[MAX_V];
	int prevv[MAX_V], preve[MAX_V];

	void init(int n)
	{
		V = n;
		for (int i = 0; i <= V; i++) G[i].clear();
	}

	void add_edge(int from, int to, int cap, int cost)
	{
		G[from].push_back((edge){to, cap, cost, (int)G[to].size()});
		G[to].push_back((edge){from, 0, -cost, (int)G[from].size() - 1});
	}

	int min_cost_flow(int s, int t, int f)
	{
		int res = 0;
		std::fill(h, h + V, 0);
		while (f > 0) {
			std::priority_queue<P, std::vector<P>, std::greater<P> > que;
			std::fill(dist, dist + V, INF);
			dist[s] = 0;
			que.push(P(0, s));
			while (!que.empty()) {
				P p = que.top(); que.pop();
				int v = p.second;
				if (dist[v] < p.first) continue;
				for (int i = 0; i < (int)G[v].size(); i++) {
					edge & e = G[v][i];
					if (e.cap > 0 && dist[e.to] > dist[v] + e.cost + h[v] - h[e.to]) {
						dist[e.to] = dist[v] + e.cost + h[v] - h[e.to];
						prevv[e.to] = v;
						preve[e.to] = i;
						que.push(P(dist[e.to], e.to));
					}
				}
			}
			if (dist[t] == INF) return -1;
			for (int v = 0; v < V; v++) h[v] += dist[v];

			int d = f;
			for (int v = t; v != s; v = prevv[v])
				d = std::min(d, G[prevv[v]][preve[v]].cap);
			f -= d;
			res += d * h[t];
			for (int v = t; v != s; v = prevv[v]) {
				edge & e = G[prevv[v]][preve[v]];
				e.cap -= d;
				G[v][e.rev].cap += d;
			}
		}
		return res;
	}
};

struct edge { int from, to, cost; };
struct tim { int h, m, s; };

std::istream & operator>>(std::istream & is, tim & a)
{
	char x, y;
	is >> x >> y; a.h = (int)(x - '0') * 10 + (int)(y - '0');
	is >> x; is >> x >> y; a.m = (int)(x - '0') * 10 + (int)(y - '0');
	is >> x; is >> x >> y; a.s = (int)(x - '0') * 10 + (int)(y - '0');
	return is;
}

std::map<int, int> dis;

int trans(tim const & a) { return a.h * 3600 + a.m * 60 + a.s; }

int main()
{
	std::ios::sync_with_stdio(false);
	int n, k;
	std::cin >> n >> k;
	std::vector<edge> input(n);

	int tick = 1;
	std::set<int> atime;
	for (int i = 0, c; i < n; i++) {
		tim t1, t2;
		std::cin >> t1 >> t2 >> c;
		int i1 = trans(t1), i2 = trans(t2);
		input[i].from = i1; input[i].to = i2; input[i].cost = c;
		atime.insert(i1);
		atime.insert(i2);
	}

	for (std::set<int>::iterator it = atime.begin(); it != atime.end(); ++it)
		dis[*it] = tick++;

	int s = 0, t = tick + 1;
	mcmf::init(t + 1);
	for (int i = 0; i < n; i++)
		mcmf::add_edge(dis[input[i].from], dis[input[i].to], 1, -input[i].cost);
	mcmf::add_edge(s, 1, k, 0);
	mcmf::add_edge(tick - 1, t, k, 0);
	for (int i = 1; i < tick - 1; i++)
		mcmf::add_edge(i, i + 1, k, 0);

	int ans = mcmf::min_cost_flow(s, t, k);
	std::cout << -ans << '\n';
}

```

{% endraw %}

