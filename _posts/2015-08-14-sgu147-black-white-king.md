---
layout: post
title: "[sgu147] Black white king"
description: ""
category: "sgu"
tags: ["细节", "枚举"]
---
{% include JB/setup %}
{% raw %}

### 题意

在一个 \\(N \times N\\) 的棋盘上，告诉你 black king, white king, black-white king 的位置，
black-white king 不被其他两个王看到（隐身），black king 和 white king 要联合对抗 black-white king,
他们需要走到相邻的位置汇合（一个格子有 \\(8\\) 个相邻的格子）。
他们将沿着所有合法汇合位置中，距离和最短的那些路走。
现在 black-white king 想阻止他们，只需要在他们相遇之前吃掉某一个 king，
当他某一次移动的时候移动到一个 king 的位置就可以算吃掉他，如果 king 走到他的位置则不算，
black-white king 移动要沿着到某点最短路走（就是走的路径不能被缩短）。

问 black-white king 是否有可能阻止其他两个王相遇，如果有可能输出他最少需要走几步吃掉其他两个王。
否则输出其他两个王最少一共走几步相遇。移动是按照 white king, black king, black-white king 的顺序进行的，
一个移动要向相邻 \\(8\\) 个棋盘内的点移动。


### 题解

非常繁琐的题，重构了两次，前两次都想错了...
因为要最短，所以在 white king 和 black king 初始位置构成的矩形中，
king 每回合，king 在长边的方向上是肯定有移动的。所以我们可以枚举移动步数 \\(i\\)，
假设他们初始构成的矩形是横边长一点，那么我们就可以知道某个王横向的坐标，
然后算出纵向坐标的范围，然后判断 black-white king 是不是能在第 \\(i\\) 步吃到就行。

假设 \\(d\\) 是一个王走到另一个王那儿的最短距离，那么 \\(d = \max(\Delta x, \Delta y)\\)，
合法的相遇点肯定是一个王走 \\([ {d \over 2} ]\\)，另一个王走 \\(d - {[ {d \over 2} ]}\\) 能相遇的点，
并且很容易知道，black-white king 每一步能到达的范围是一个正方形边界。
如果最后 black-white king 不能赢，那么两王相遇最小距离和是 \\(d - 1\\)。


### 实现

```cpp

#include <iostream>
#include <cmath>
#include <utility>

typedef std::pair<int, int> coord_type;

std::istream & operator>>(std::istream & is, coord_type & a)
{
	is >> a.first >> a.second;
	return is;
}

int const inf = 1 << 28;
coord_type da[3];
int n, dis;

int max_dis(coord_type const & a, coord_type const & b)
{
	return std::max(std::abs(a.first - b.first), std::abs(a.second - b.second));
}

bool on_edge(coord_type const & a, int step)
{
	return max_dis(a, da[2]) == step;
}

bool insquare(coord_type const & a, int step)
{
	return max_dis(a, da[2]) < step;
}

bool cross(coord_type const & a, coord_type const & b, int step)
{
	return (insquare(a, step) && !insquare(b, step)) || (insquare(b, step) && !insquare(a, step));
}

int check(coord_type const & a, coord_type const & b, int step)
{
	int dh = (a.second < b.second) ? 1 : -1; // direct horizontally
	for (int i = 1; i <= step; i++) {
		coord_type p[2];
		p[0].second = p[1].second = a.second + dh * i;
		p[0].first = std::max(1, std::max(a.first - i, b.first - (dis-i)));
		p[1].first = std::min(n, std::min(a.first + i, b.first + (dis-i)));
		if (on_edge(p[0], i) || on_edge(p[1], i) || cross(p[0], p[1], i))
			return i;
	}
	return inf;
}

int main()
{
	std::cin >> n;
	for (int i = 0; i < 3; i++) std::cin >> da[i];

	if (std::abs(da[0].first - da[1].first) > std::abs(da[0].second - da[1].second))
		for (int i = 0; i < 3; i++)
			std::swap(da[i].first, da[i].second);

	dis = std::abs(da[0].second - da[1].second);
	int ans = std::min(check(da[0], da[1], dis/2 - 1), check(da[1], da[0], dis/2 - 1));
	if (ans < inf)
		std::cout << "YES\n" << ans << '\n';
	else
		std::cout << "NO\n" << dis - 1 << '\n';
}

```

{% endraw %}

