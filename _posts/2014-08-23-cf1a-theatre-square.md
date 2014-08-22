---
layout: post
title: "[cf1a] Theatre Square"
description: ""
category: "codeforces"
tags: ["codeforces", "数学"]
---
{% include JB/setup %}

### 题意
已知一个\\(n \times m\\)的矩形，用\\(a \times a\\)的正方形覆盖，问最少需要几块。

### 题解
简单。
$$ \mathrm{Ans} = \Big\lceil {m \over a} \Big\rceil \cdot \Big\lceil {n \over a} \Big\rceil $$

若是整数相除再上取整，可以这样实现

```cpp
int ceil(int x, int y)
{
	// ceil可以有用以下三种实现，实测效率相当
	// return x/y + (x % y != 0);
	// return (x + y - 1)/y;
	return x/y + !!(x%y);
}
```
浮点数相除的话用[std::ceil][1]

### 实现

```cpp
#include <iostream>
using namespace std;

int main()
{
	int n, m, a;
	cin >> n >> m >> a;
	cout << (long long)((m+a-1) / a) * (long long)((n+a-1) / a);
}
```

[1]: http://en.cppreference.com/w/cpp/numeric/math/ceil

