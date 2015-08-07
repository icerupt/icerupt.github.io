---
layout: post
title: "[sgu137] Funny Strings"
description: ""
category: "sgu"
tags: ["论文题", "欧拉串"]
---
{% include JB/setup %}

{% raw %}

### 题意

给出一个欧拉串的定义，给定 \\(N, K\\)，且保证 \\(\gcd(N, K) = 1\\)，请构造出一个符合条件的欧拉串。

### 题解

这是论文题啊，满足这个互质的条件一定能且只能构造唯一的欧拉串。
请膜拜这篇[论文][1]。

一些性质还是很好推的，比如所有数字只会差 \\(1\\)

```
\(a \times b \)

function E-STRING (n; k :Z+) : Stringover N;
if k < n → return(exp(E-STRING (n − k; k)))
[] k = n → return(‘1’);
[] k > n → return(inc(E-STRING (n; k − n)))
```

### 实现

```cpp

#include <iostream>
#include <vector>

void print(std::vector<int> const & a)
{
	std::cout << a[0];
	for (int i = 1; i < (int)a.size(); i++)
		std::cout << ' ' << a[i];
	std::cout << '\n';
}

std::vector<int> unit(1, 1);

std::vector<int> inc(std::vector<int> a)
{
	for (int i = 0; i < (int)a.size(); i++)
		a[i]++;
	return a;
}

std::vector<int> exp(std::vector<int> const & a)
{
	std::vector<int> tmp;
	for (int i = 0; i < (int)a.size(); i++) {
		tmp.push_back(0);
		for (int j = 0; j < a[i]; j++) tmp.push_back(1);
	}
	return tmp;
}

std::vector<int> e_string(int n, int k)
{
	if (k < n) return exp(e_string(n - k, k));
	if (k > n) return inc(e_string(n, k - n));
	return unit;
}

int main()
{
	int n, k;
	std::cin >> n >> k;
	std::vector<int> euclid_string = e_string(n, k);
	print(euclid_string);
}

```

[1]: http://www.cis.uoguelph.ca/~sawada/papers/euclid.pdf

{% endraw %}

