---
layout: post
title: "[bzoj1908] Color a Tree"
description: ""
category: "bzoj"
tags: ["贪心", "证明"]
---
{% include JB/setup %}
{% raw %}

### 题意

给定一个树，每个点有个权值 \\(c\_i\\)，现在要给所有点染色，如果要染当前点
其父亲节点必须以经染色，第 \\(t\_i\\) 个染的点，需费用为 \\(t\_i \times c\_i\\)。
现在要染所有点并且最小化 \\(\displaystyle\sum t\_i \times c\_i \\)。


### 题解

这题是个好题。用贪心来做，先说做法，再形象的证明一下。

1. 在非根节点中找到权值最大点，如果其父亲节点染了，那么紧跟着会染当前点。所以可以将两个点合并;
2. 将两个点合并所得到的新的点的权值，是合并集合里所有点权和除以点的个数;
3. 合并按照次序合并，将儿子合并到父亲点集的尾部。最后合并到只剩下根节点，那个次序就是答案。

第一、三条都很好理解，第一条的贪心很显然，难点就在于第二条。我们尝试着来证明一下。

![v](/assets/img/bzoj1908-tree.svg)
<div class="figure-comment">
图中圈内数字是顶点编号，假设根节点 0 的权值为 \\(0\\)，1 号点的权值为 \\(x\\)，
2 号节点是由 \\(t\\)个点（按照其合并好后的次序权值为 \\(c\_i\\)）组成的合并后的点。
</div>

现在假设合并到这样一个局面，那么接下来要考虑合并节点0和节点2或者是合并节点0和节点，
假设节点1的权值 \\(x\\) 是个给定的值，那么按照我们的贪心算法，怎么样合并就在于我们
怎么估价节点2这个已经进行一些合并的节点的权值。

那么我们不妨把两种情况都写出来，如果选择合并0和1, 那么最终权和：
$$ cost\_1 = 1 \times 0 + 2 \times x + \displaystyle\sum_{i=1}^{t} (i + 2) \times c\_i \label{one} $$

如果选择合并0和2, 那么最终权和：
$$ cost\_2 = 1 \times 0 + \displaystyle\sum_{i=1}^{t} (i + 1) \times c\_i + (t + 2) \times x \label{two} $$

那么在 \\(x\\) 取到与估价权值相等的时候 (\ref{one}) 和 (\ref{two}) 相等，可以解出：
$$ x = \frac{ \displaystyle\sum_{i=1}^{t} c\_i }{ t } $$

就这么巧妙的，你就得到了合并后节点的估价函数！


### 实现

```cpp
#include <cstdio>
#include <cstring>
#include <vector>

int const maxn = 1007;
bool merged[maxn];
int father[maxn];
int cost[maxn];
int cost_back[maxn];
int size[maxn];

int head[maxn], tail[maxn], next[2 * maxn], end_point[2 * maxn];
int alloc;

int n, r;

std::vector<int> graph[maxn];

void dfs(int u, int f)
{
	father[u] = f;
	for (int i = 0; i < (int)graph[u].size(); i++) {
		int v = graph[u][i];
		if (v == f) continue;
		dfs(v, u);
	}
}

int main()
{
	while (std::scanf("%d %d", &n, &r) && (n || r)) {
		alloc = 1;
		r--;
		std::memset(merged, 0, sizeof(merged));
		std::memset(head, 0, sizeof(head));
		std::memset(next, 0, sizeof(next));
		std::memset(tail, 0, sizeof(tail));
		for (int i = 0; i < n; i++) graph[i].clear();

		for (int i = 0; i < n; i++) {
			std::scanf("%d", &cost[i]);
			cost_back[i] = cost[i];
			size[i] = 1;
		}

		for (int i = 1, x, y; i < n; i++) {
			std::scanf("%d %d", &x, &y);
			x--;  y--;
			graph[x].push_back(y);
			graph[y].push_back(x);
		}

		dfs(r, -1);

		for (int i = 0; i < n-1; i++) {
			int mole = 0, deno = 1, pos; // mole / deno
			for (int j = 0; j < n; j++) {
				if (merged[j] || j == r) continue;
				if (mole * size[j] < deno * cost[j]) {
					mole = cost[j];
					deno = size[j];
					pos = j;
				}
			}

//			std::printf("--> %d %d\n", pos, cost[pos]);

			// merge
			merged[pos] = true;
			int f = father[pos];
			while (merged[f]) f= father[f];
			cost[f] += cost[pos];
			size[f] += size[pos];
			end_point[alloc] = pos;
			next[alloc] = head[pos];
			if (tail[f]) next[tail[f]] = alloc;
			else		 head[f] = alloc;
			tail[f] = alloc++;
			if (tail[pos]) tail[f] = tail[pos];
		}

		int ans = cost_back[r], ti = 2;
		for (int p = head[r]; p; p = next[p], ti++) {
			//std::printf("--> %d\n", end_point[p]);
			ans += ti * cost_back[end_point[p]];
		}
		std::printf("%d\n", ans);
	 }
}

```

{% endraw %}

