---
layout: post
title: "[cf1a] Theatre Square"
description: ""
category: codeforces
tags: ["codeforces", "数学"]
---
{% include JB/setup %}

###题意
已知一个\\(n \times m\\)的矩形，用\\(a \times a\\)的正方形覆盖，问最少需要几块。

###题解
比较简单。
$$ \mathrm{Ans} = \Big\lceil {m \over a} \Big\rceil \cdot \Big\lceil {n \over a} \Big\rceil $$

整数的话可以这么实现

```cpp
int ceil(int x, int y)
{
	// ceil可以有用以下三种实现，实测效率相当
	// return x/y + (x % y != 0);
	// return (x + y - 1)/y;
	return x/y + !!(x%y);
}
```
浮点数相除的话还是用[std::ceil](http://en.cppreference.com/w/cpp/numeric/math/ceil)

