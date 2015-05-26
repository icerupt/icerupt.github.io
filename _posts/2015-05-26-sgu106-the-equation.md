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
对于方程\\(a x + b y + c = 0\\)，已知整数\\(a, b, c\\), 求方程满足\\(x1 \leq x \leq x2\\)且\\(y1 \leq y \leq y2\\)的条件下，
整数解(即解\\(x, y\\)都是整数)的个数。

### 题解
唉，深夜刷题伤不起啊，虽然理论上挺水的，各种细节啊。不过收获也不少。<del>睡醒了再补上吧。</del>

对于\\(a b=0\\)特殊判断就好，不然就先用[extended euclid][1]求得一组解，然后判断在区间内的整点个数就ok。
然后就是很傻的把[round][2]当作[floor][3]用。。顺带一提，c++里，浮点型向int的转化，不是floor而是截断取整。

最后就是一个奇葩错误：
```
error: redefinition of 'y1' as different kind of symbol

/usr/include/bits/mathcalls.h:241:13: note: previous definition is here
	__MATHCALL (y1, , (_Mdouble_));
```
我定义了一个叫y1的变量，恩，`using namespace std;` 了。。我们当然管不着厂商起什么变量名，
接下来试着都打`std::`看看手感吧。

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
			t1 = (y_1 - y)/(double)ap;  t2 = (y2 - y)/(double)ap;
			if (ap > 0) { kk1 = ceil(-t2); kk2 = floor(-t1); }
			else        { kk1 = ceil(-t1); kk2 = floor(-t2); }
			ans = intersect(k1, k2, kk1, kk2);
		}
	}
	cout << ans << "\n";
}

```

[1]: http://en.wikipedia.org/wiki/Extended_Euclidean_algorithm
[2]: http://en.cppreference.com/w/cpp/numeric/math/round
[3]: http://en.cppreference.com/w/cpp/numeric/math/floor

{% endraw %}

