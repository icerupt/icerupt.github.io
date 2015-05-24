---
layout: post
title: "[poj2458] Rigging the Bovine Election"
description: ""
category: "poj"
tags: ["位运算", "常数优化"]
---
{% include JB/setup %}

### 题意
求一个\\(5\*5\\)的矩阵中，点数为7的且其中\\(J\\)比\\(H\\)多的联通块的个数，
一个点相领的点是与上下左右四个点。

### 题解
只要暴力枚举取了哪7个点，并判断其中\\(J, H\\)的个数以及是不是联通。
各种位运算优化啊，代码中的\\(int bit\_count\_1(int)\\)是计算二进制中1的个数。
最后发现只要把queue改成raw array效率就大大提升了。
另外poj的服务器真的渣啊，在[BZOJ][1]上用queue也能过。
花了好多时间。。。。

### 实现
```cpp
{{"
#include <iostream>
#include <queue>
using namespace std;

struct xy { int x, y; };
int const opt[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};
int graph[26][26];
int n = 5, m = 5, map;

int bit_count_1(int x)
{
	x = (x & 0x55555555) + ((x>>1) & 0x55555555);
	x = (x & 0x33333333) + ((x>>2) & 0x33333333);
	x = (x & 0x0F0F0F0F) + ((x>>4) & 0x0F0F0F0F);
	x = (x & 0x00FF00FF) + ((x>>8) & 0x00FF00FF);
	x = (x & 0x0000FFFF) + ((x>>16)& 0x0000FFFF);
	return x;
}

bool inrange(int x, int y)
{
	return (0 <= x && x < 5) && (0 <= y && y < 5);
}


int q[26];

bool judge(int x)
{
	if (bit_count_1(x & map) > 3) return false;

	int head = 0, tail = 0, last1 = 0;
	for (int i = x; !(i&1); i >>= 1) last1++;
	q[tail++] = last1;
	int vis = 1<<last1;

	while (head < tail) {
		int u = q[head++];
		for (int i = 1; i <= graph[u][0]; i++) {
			int v = graph[u][i];
			if (((1<<v)&vis) || !((1<<v)&x)) continue;

			vis |= 1<<v;
			q[tail++] = v;
		}
	}
	if (tail < 7) return false;
	return true;
}

void init_graph()
{
	for (int i = 0; i < n; i++) {
		for (int j = 0; j < m; j++) {
			int u = i*n + j;
			for (int k = 0; k < 4; k++) {
				int px = i + opt[k][0], py = j + opt[k][1];
				if (inrange(px, py)) {
					int v = px*n + py;
					graph[u][++graph[u][0]] = v;
				}
			}
		}
	}
}

int main()
{
	init_graph();

	for (int i = 0; i < 25; i++) {
		char ch; cin >> ch;
		if (ch == 'H') map += 1<<i;
	}

	int ans = 0;
	for (int i1 = 0; i1 < 19; i1++) {
		int t1 = 1<<i1;
		for (int i2 = i1+1; i2 < 20; i2++)
		for (int i3 = i2+1; i3 < 21; i3++) {
			int t3 = t1 + (1<<i2) + (1<<i3);
			for (int i4 = i3+1; i4 < 22; i4++)
			for (int i5 = i4+1; i5 < 23; i5++) {
				int t5 = t3 + (1<<i4) + (1<<i5);
				for (int i6 = i5+1; i6 < 24; i6++) {
					int t6 = t5 + (1<<i6);
					for (int i7 = i6+1; i7 < 25; i7++) {
						int t7 = t6 + (1<<i7);
						if (judge(t7)) ans++;
					}
				}
			}
		}
	}
	cout << ans << "\n";
}
" | escape }}

```
[1]: http://www.lydsy.com/JudgeOnline/problem.php?id=1675

