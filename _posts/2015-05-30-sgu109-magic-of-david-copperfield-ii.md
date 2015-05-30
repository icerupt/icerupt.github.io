---
layout: post
title: "[sgu109] Magic of David Copperfield II"
description: ""
category: "sgu"
tags: ["找规律", "构造"]
---
{% include JB/setup %}

{% raw %}

### 题意

给定一个 \\(n \\times n\\) 的方形，并且从左到右从上到下标号 \\(1~n\\)，
开始时你的手在 \\(1\\) 号位置，然后在第 \\(i\\) 轮，魔术师会让你移动 \\(k\_i\\) 步，
移动合法的一步就是移动到上下左右相邻的还存在的一个位置上，移动完成后，会消失若干标号的格子，
当然需要保证，消失的格子不会是你手在的位置。经过若干轮游戏后，只剩下一个一个格子，你的手在那个格子上。
现在要让你构造一个方案，使得这个游戏能达到预期效果，每轮 \\(k\_i(n \\leq k\_i < 300)\\) 都不相同，
且要保证每轮至少有一个格子消失。

### 题解

黑白染色之后，如果每次走奇数个格子，一定会走到另一个颜色的格子上。所以我们可以构造性的，
如果 \\(n\\) 是奇数，每次就把最外面一圈去掉，如果是 \\(n\\) 是偶数，就先想办法变成奇数。

google了一下发现还有更简单的构造方法，就是每次把右下角那个副对角线方向的斜线去掉，
最后到 \\(1\\) 就结束。这个表达可能有点。。但是我不想画图了。

### 实现

```cpp
#include <iostream>
#include <vector>

int const maxn = 103;
int pos[maxn][maxn];
int n;

std::vector<std::vector<int> > ans;
std::vector<int> way;

void reduce(int start, int end, int x)
{
	for (int i = start; i < end; i += 2) {
		way.push_back(pos[x][i]); way.push_back(pos[i][x]);
	}
}

void decrease_bound(int len, int x, int move)
{
	if (len == 1) return;
	int y = x + len - 1;

	way.clear(); way.push_back(move);
	way.push_back(pos[x][x]); way.push_back(pos[x][y]);
	way.push_back(pos[y][x]); way.push_back(pos[y][y]);
	reduce(x+2, y, x); reduce(x+2, y, y);
	ans.push_back(way);

	way.clear(); way.push_back(move+2);
	reduce(x+1, y, x); reduce(x+1, y, y);
	ans.push_back(way);

	decrease_bound(len-2, x+1, move+4);
}

void shrink_to_odd(int len, int x, int move)
{
	int y = x + len - 1;
	way.clear(); way.push_back(move);
	way.push_back(pos[y][y]);
	ans.push_back(way);

	way.clear(); way.push_back(move+1);
	reduce(x+1, y, y);
	ans.push_back(way);

	way.clear(); way.push_back(move+3);
	reduce(x, y, y);
	ans.push_back(way);

	decrease_bound(len-1, x, move+5);
}

int main()
{
	std::cin >> n;
	if (n == 2) { std::cout << "2 2\n3 1 4\n"; return 0; }
	for (int i = 1; i <= n; i++)
		for (int j = 1; j <= n; j++) pos[i][j] = (i-1)*n + j;
	if (!(n&1)) shrink_to_odd(n, 1, n);
	else        decrease_bound(n, 1, n);

	for (int i = 0; i < int(ans.size()); i++) {
		std::cout << ans[i][0];
		for (int j = 1; j < int(ans[i].size()); j++)
			std::cout << " " << ans[i][j];
		std::cout << "\n";
	}
}

```

{% endraw %}

