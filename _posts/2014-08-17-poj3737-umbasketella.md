---
layout: post
title: "[poj3737] UmBasketella"
description: ""
category: 
tags: []
---
{% include JB/setup %}

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


$$ s = pi r l + pi * r^2 $$
$$ v = {1 \over 3} \pi h r^2 $$
$$ l = \sqrt{h^2 + r^2} $$
$$ h = {\sqrt{s^2 - 2 \pi s r^2} \over \pi r} $$
$$ v = {1 \over 3} r \sqrt{s^2 - 2 \pi s r^2} $$
$$ {\mathrm d v \over \mathrm d r}  = {1 \over 3} \sqrt{s^2 - 2 \pi s r^2} - {2 \over 3} {\pi s r \over \sqrt{s^2 - 2 \pi s r^2}} $$
$$ {\mathrm d v \over \mathrm d r}  = 0 $$
$$ r = \sqrt{s \over 4 \pi} $$

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
