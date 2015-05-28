---
layout: post
title: "[sgu108] Self numbers 2"
description: ""
category: "sgu"
tags: ["数学", "找规律", "有待深入研究"]
---
{% include JB/setup %}

{% raw %}

### 题意

定义 \\(d(n) = n + sum(n)\\), \\(sum(n)\\) 表示把 \\(n\\) 的每位求和。
并且称 \\(d(n)\\) 由 \\(n\\) 产生，如果一个数不能由任何其他数产生，则称其为 self-number。
现在要求 \\([1, N]\\) 的 self-number 的个数，并且输出第 \\(s\_i(1 \\leq i \\leq K)\\) 个 self-number。

### 题解

可以 \\(O(n)\\) 模拟，但是怕常数，试着找了下规律，将 self-numbers 相邻的做差输出，
发现了一些神奇的规律。

```
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 11 2
11 11 11 11 11 11 11 11 15
11 11 11 11 11 11 11 11 2
```

大概差值为呈现大概这样一个 square，并重复出现。那个 \\(15\\) 是一个 magic-number，不断变化，
大概每 \\(10\\) 组变成 \\(28\\)，然后以 \\(28\\) 为循环节每 \\(10\\) 组最后一个 \\(28\\) 又变成 \\(41\\)，
然后是 \\(54...\\) 到 \\(54\\) 就够题目范围了。这样讲可能比较抽象，打印出来看看就知道了。

然后关于这个 square 还有写神奇的性质，比如说每个 \\(15\\) 为循环节的 square 的和是 \\(1001\\)，
以 \\(28\\) 为循环节的和是 \\(10001\\)，然后是 \\(100001, 1000001...\\) 还有每个循环节的数字个数也很有规律。
应该就是那个 magic-number 来保持这些性质的吧。总之很神奇。

个数用二分先求出来，接下来就是读一个 \\(s\_i\\) 求一个 self-number 了，效率非常高。

### 实现

```cpp
#include <iostream>
#include <vector>

int n, k;

int last_square(int t, int last_line, int magic_num)
{
	int start = 0;
	if (t <= 80) {
		start += int(t/10) * 101;
		start += (t%10)*11;
	} else {
		start += 808; t -= 80;
		int s = 11*last_line + magic_num;
		if (t <= last_line)     start += 11*t;
		else
		if (t == last_line+1)   start += s;
		else
		if (t <= last_line*2+1) start += s + 11*(t-last_line-1);
		else                    start += s + 11*last_line + 2;
	}
	return start;
}

int calc(int t)
{
	if (t <= 5) return (t-1)*2 + 1;
	else
	if (t <= 15) {
		int start = 9; t -= 5;
		if (t <= 9) start += 11*t;
		else        start += 101;
		return start;
	}
	else {
		int start = 110; t -= 15;

		start += int(t/97778) * 1000001; t %= 97778;
		int tot = 97778 - 92;
		if (t > tot) {
			start += 100001*9 + 10001*9 + 1001*9;
			start += last_square(t - tot, 5, 54);
		} else {
			start += int(t/9778) * 100001; t %= 9778;
			tot = 9778 - 94;
			if (t > tot) {
				start += 10001 * 9 + 1001 * 9;
				start += last_square(t - tot, 6, 41);
			} else {
				start += int(t/978) * 10001; t %= 978;
				tot = 978 - 96;
				if (t > tot) {
					start += 1001*9;
					start += last_square(t - tot, 7, 28);
				} else {
					start += int(t/98) * 1001; t %= 98;
					start += last_square(t, 8, 15);
				}
			}
		}
		return start;
	}
}

int num_self(int x)
{
	int l = 1, r = int(x/10) + 100;
	while (l+1 < r) {
		int mid = (l+r)/2;
		if (calc(mid) < n) l = mid+1;
		else               r = mid;
	}
	int nl = calc(l), nr = calc(r);
	if (nl == n) return l;
	if (nr == n) return r;
	if (nl > n)  return l-1;
	return r-1;
}

int main()
{
	std::ios::sync_with_stdio(false);
	std::cin >> n >> k;
	std::cout << num_self(n) << "\n";
	int tmp; std::cin >> tmp;
	std::cout << calc(tmp);
	for (int i = 1; i < k; i++) {
		std::cin >> tmp;
		std::cout << " " << calc(tmp);
	}
}

```

{% endraw %}

