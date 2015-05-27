---
layout: post
title: "[poj3660] Cow Contest"
description: ""
category: "poj"
tags: ["传递性", "dfs", "floyd"]
---
{% include JB/setup %}

{% raw %}

### 题意
要判断 \\(n(n \\leq 100)\\) 个奶牛，能确定编程能力排名的奶牛的个数。已知 \\(m\\) 对奶牛编程能力的强弱关系。
编程能力强弱满足[严格偏序][1]。

### 题解
一开始用拓扑排序搞，后来发现其实想简单了。想想数据范围也是。。可以用 floyd 先确定所有人的强弱关系。
然后对于每个人统计比他强的人的个数，最后排个序，假设为 \\(d\\)，若 \\(d\_i = i\\) 且 \\(d\_i\\)是唯一的，
那么这头奶牛可以确定排名。

其实也可以有一个 \\(O(n^2)\\) 的，就是对于每头牛，对正向图以及反向图做dfs，
如果能把所有牛都访问到，那么他排名也是可以确定的。

### 实现
```cpp
#include <iostream>
#include <algorithm>
#include <vector>

int const maxn = 102;

bool map[maxn][maxn];
int greater[maxn];

int n, m;

int main()
{
	std::cin >> n >> m;
	for (int i = 0, x, y; i < m; i++) {
		std::cin >> x >> y;
		map[--x][--y] = true;
	}

	for (int k = 0; k < n; k++)
		for (int i = 0; i < n; i++)
			for (int j = 0; j < n; j++)
				if (map[i][k] && map[k][j]) map[i][j] = true;

	std::vector<int> greater_count;
	for (int i = 0; i < n; i++) {
		int count = 0;
		for (int j = 0; j < n; j++) if (map[j][i]) count++;
		greater_count.push_back(count);
		greater[count]++;
	}
	sort(greater_count.begin(), greater_count.end());
	int ans = 0;
	for (int i = 0; i < n; i++)
		if (greater_count[i] == i && greater[i] == 1) ans++;
	std::cout << ans << "\n";
}

```

[1]: http://zh.wikipedia.org/wiki/%E5%81%8F%E5%BA%8F%E5%85%B3%E7%B3%BB#.E4.B8.A5.E6.A0.BC.E5.81.8F.E5.BA.8F.EF.BC.8C.E5.8F.8D.E8.87.AA.E5.8F.8D.E5.81.8F.E5.BA.8F
{% endraw %}
