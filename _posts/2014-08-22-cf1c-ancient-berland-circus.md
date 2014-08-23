---
layout: post
title: "[cf1c] Ancient Berland Circus"
description: ""
category: "codeforces"
tags: ["几何", "数学", "有待深入研究","codeforces"]
---
{% include JB/setup %}

### 题意
已知三个点坐标，求顶点包含这三个点的等角正多边形的最小面积。

### 题解
由于最后的图形是正多边形所以所有的顶点一定都外接于同一个外接圆上。

![v](/assets/img/cf1c-c.png)
<div class="figure-comment">
红点是已知点，图中仅是一种可能的正多边形情况。
</div>


同一个圆内的内接正多边形边数越少面积越小，我们只需要求最小的边数或者说最小的x
角。我们先算出一些需要知道的量。已知三点构成的三角形，易知三个角\\(A, B, C\\)
和三条边\\(a, b, c\\)。

由正弦定理我们能算出外接圆半径\\(r\\)
$$ r = {a \over 2 \sin(A)} \nonumber$$

现在三角形每条边都对应一个圆心角\\(2A, 2B, 2C\\),最后的正多边形的每条边也
对应一个圆心角，想要最后边数最少，就要使圆心角尽量大。又最后正多边形每条边对
应的圆心角是通过划分现在三角形三边对应的圆心角得到的，且一定是整数倍划分。容
易看出最后的圆心角大小就是现在三角形三边对应圆心角的最大公约数，故最后的边数\\(n\\)
$$ n = {2 \pi \over \mathrm{gcd}_{\mathbb R}(2A, 2B, 2C)} \nonumber $$
也即
$$ n = {\pi \over \mathrm{gcd}_{\mathbb R}(A, B, C)} \nonumber $$
最后的面积\\(s\\)为
<div class="em">
$$ s = {n \cdot {1 \over 2} \sin({2 \pi \over n}) r^2} \nonumber $$
</div>

值得注意的是\\(\mathrm{gcd}_{\mathbb R}(A, B, C)\\)中的
\\(A, B, C \in \mathbb R\\)

事实上只要\\(A \over B\\)是有理数就能求出他们的[实数最大公约数][1]。
应用于实数的\\(\mathrm{gcd}_{\mathbb R}\\)的目的是求出实数\\(g\\)使得
\\(A = m g, B = n g\\)，其中\\(m, n\\)都是整数。

我们可以通过有理数的特例来加以理解。只要将两个有理数通分成同分母的分数再求分子的gcd
最后除以分母就好。我们也没必要通分的这么仔细，比如1.23和2.333可以同时乘以1000
变成1230和2333求最大公约数再除以1000。

更详细的实数情况有[待深入研究][2]。

\\(gcd\_double\\)实现如下

```cpp
double gcd(double x, double y)
{
	while (fabs(x) > eps && fabs(y) > eps) {
		if (x > y)
			x -= floor(x / y) * y;
		else
			y -= floor(y / x) * x;
	}
	return x + y;
}
```

### 实现

```cpp
#include <iostream>
#include <cmath>
#include <iomanip>
using namespace std;

const double eps = 1e-5;

double dist(double x1, double y1, double x2, double y2);
double angle(double a, double b, double c);
double gcd(double x, double y);

int main()
{
	double x1, y1, x2, y2, x3, y3;
	cin >> x1 >> y1 >> x2 >> y2 >> x3 >> y3;
	double a = dist(x1, y1, x2 , y2);
	double b = dist(x1, y1, x3 , y3);
	double c = dist(x2, y2, x3 , y3);
	double A = angle(a, b, c);
	double B = angle(b, a, c);
	double C = angle(c, a, b);
	double r = a/(2*sin(A));
	double n = M_PI / gcd(gcd(A, B), C);
	double s = n / 2 * r * r * sin(2 * M_PI / n);
	cout << setprecision(6) << fixed << s << endl;
}

double dist(double x1, double y1, double x2, double y2)
{
	return sqrt((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1));
}

double angle(double a, double b, double c)
{
	return acos((c*c + b*b - a*a) / (2*b*c));
}

double gcd(double x, double y)
{
	while (fabs(x) > eps && fabs(y) > eps) {
		if (x > y)
			x -= floor(x / y) * y;
		else
			y -= floor(y / x) * x;
	}
	return x + y;
}

```
[1]:http://en.wikipedia.org/wiki/Euclidean_algorithm#Rational_and_real_numbers
[2]:http://icerupt.github.io/tags.html#有待深入研究-ref

