---
layout: post
title: "[sgu106] The equation"
description: ""
category: "sgu"
tags: ["数学", "线性不定方程", "有待深入研究", "细节"]
---
{% include JB/setup %}

{% raw %}

### 题意
已知方程\\(a x + b y + c = 0\\)的个整系数，求方程在\\((x1, y1)\\)到\\((x2, y2)\\)的坐标范围内，
整数解(即解\\(x, y\\)都是整数)的个数。

### 题解
唉，深夜刷题伤不起啊，虽然理论上挺水的，各种细节啊。不过收获也不少，睡醒了再补上吧。

### 实现
```cpp
#include <iostream>
#include <cmath>
using namespace std;

long long a, b, c, x1, y_1, x2, y2, ans;

long long intersect(long long x1, long long x2, long long x3, long long x4)
{
	if (x1 > x3) { swap(x1, x3); swap(x2, x4); }
	if (x3 <= x2) {
		long long r = min(x2, x4);
		return r - x3 + 1;
	} else return 0;
}

long long gcd(long long a, long long b)
{
	return b ? gcd(b, a%b) : a;
}

void extend_gcd(long long a, long long b, long long & d, long long & x, long long & y)
{
	if (!b) { d = a; x = 1; y = 0; }
	else {
		extend_gcd(b, a%b, d, y, x);
		y -= x * (a/b);
	}
}

int main()
{
	cin >> a >> b >> c >> x1 >> x2 >> y_1 >> y2;
	if (x2 < x1 || y2 < y_1) { cout << 0 << "\n"; return 0; }
	if (a == 0 && b != 0) {
		double yy = -c/b;
		if (y_1 <= yy && yy <= y2) ans = x2 - x1 + 1;
	} else
	if (b == 0 && a != 0) {
		double xx = -c/a;
		if (x1 <= xx && xx <= x2) ans = y2 - y_1 + 1;
	}
	else
	if (a == 0 && b == 0) {
		if (c == 0)
			ans = (y2 - y_1 + 1) * (x2 - x1 + 1);
	} else {
		long long x, y, d = gcd(a, b);
		c = -c;
		if (!(c%d)) {
			extend_gcd(a, b, d, x, y);
			x = c/d * x;  y = c/d * y;
			long long ap = a/d, bp = b/d, k1, k2, kk1, kk2;
			double t1 = (x1 - x)/(double)bp, t2 = (x2 - x)/(double)bp;
			if (bp < 0) { k1 = ceil(t2); k2 = floor(t1); }
			else        { k1 = ceil(t1); k2 = floor(t2); }
			if (k1 > k2) { cout << "0\n"; return 0; }
			t1 = (y_1 - y)/(double)ap;  t2 = (y2 - y)/(double)ap;
			if (ap > 0) { kk1 = ceil(-t2); kk2 = floor(-t1); }
			else        { kk1 = ceil(-t1); kk2 = floor(-t2); }

			if (kk1 > kk2) { cout << "0\n"; return 0; }
			ans = intersect(k1, k2, kk1, kk2);
		}
	}
	cout << ans << "\n";
}

```

{% endraw %}

