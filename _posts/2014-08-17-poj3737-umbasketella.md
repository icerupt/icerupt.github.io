---
layout: post
title: "[poj3737] UmBasketella"
description: ""
category: poj
tags: ["数学", "poj"]
---
{% include JB/setup %}

### 题意

已知一个圆锥体的表面积（包括底面），求最大体积及对应圆锥体的高和底面半径。

### 题解

\\(r\\) 为底圆半径，\\(s\\) 为圆锥表面积（包括底面），\\(h\\) 为圆锥高，
\\(v\\) 为圆锥体积，\\(l\\) 为母线。

首先可以列出圆锥表面积 \\(s\\) 以及母线 \\(l\\)
$$ s = \pi r l + \pi  r^2 \label{area} $$
$$ l = \sqrt{h^2 + r^2} \label{lateral}$$

将 (\ref{lateral}) 带入 (\ref{area}) 可求得圆锥高 \\(h\\)
$$ h = {\sqrt{s^2 - 2 \pi s r^2} \over \pi r \label{height}} $$

又圆锥体积 \\(v\\) 满足
$$ v = {1 \over 3} \pi h r^2 \label{volume}$$

将 (\ref{height}) 带入 (\ref{volume}) 可得
$$ v = {1 \over 3} r \sqrt{s^2 - 2 \pi s r^2} \label{volume2} $$

![v](/assets/img/poj3737-v.png)
<div class="figure-comment">
方程 (\ref{volume2}), 由图易知只有一个合理的最值点
</div>

两边对 \\(r\\) 求导，得
$$ {\mathrm d v \over \mathrm d r}  = {1 \over 3} \sqrt{s^2 - 2 \pi s r^2} - {2 \over 3} {\pi s r \over \sqrt{s^2 - 2 \pi s r^2}} $$

解驻点
$$ {\mathrm d v \over \mathrm d r}  = 0 $$

解得的 \\(r\\) 即为最大体积下底面的半径，再利用 \\(r\\) 推出 \\(h\\) 和 \\(v\\) 即可
<div class="em">
$$ r = \sqrt{s \over 4 \pi} \nonumber $$
$$ h = {\sqrt{s^2 - 2 \pi s r^2} \over {\pi r}} \nonumber $$
$$ v = {r \over 3}  \sqrt{s^2 - 2 \pi s r^2} \nonumber $$
</div>

### 实现

```cpp
#include <iostream>
#include <cmath>
#include <iomanip>
using namespace std;

int main()
{
	double s;
	while (cin >> s) {
		double r = sqrt(s / (M_PI * 4) );
		double h = sqrt(s*s - 2*s*M_PI*r*r) / (M_PI * r);
		double v = sqrt(s*s - 2*s*M_PI*r*r) * r / 3;
		cout << setprecision(2) << fixed <<
			v << endl << h << endl << r << endl;
	}
}
```

<!--
l, h, r
s = pi r l + pi * r^2; ...1
v = 1/3 * pi r^2 h  ...2

l = sqrt(h^2 + r^2)  ...3
3 -> 1
h = sqrt(s^2 - 2 s pi r^2)/(pi r) ...4
h -> 2
v = 1/3 * r * sqrt(s^2 - 2 pi s r^2)
v' = 
由 v' = 0
r = sqrt(s/(4 pi))
-->
