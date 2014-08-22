---
layout: post
title: "[cf1b] Spreadsheets"
description: ""
category: "codeforces"
tags: ["字符串"，"模拟"]
---
{% include JB/setup %}

### 题意
将题意中描述的两种定位表格的方式相互转化。一种是StrX, 另一种是RXCY。其中
Str是Y的字母串表示发，A..Z表示1..26，可以看成26进制。

### 题解
模拟就好。读入的时候可以用[std::sscanf][1]实现匹配。
[cjxgm用c++11的正则库实现][2], codeforces似乎不支持，仅供围观。

### 实现
#include <cstdio>
#include <string>
using namespace std;

void print(int c);

int main()
{
	int n;
	for (scanf("%d", &n); n; --n) {
		char s[100];
		scanf("%s", s);
		int r, c;
		if (sscanf(s, "%*c%d%*c%d", &r, &c) == 2) {
			print(c);
			printf("%d\n", r);
		} else {
			char *p = s;
			for (c = 0; *p >= 'A'; ++p) c = c * 26 + (*p ^ 64);
	        printf("R%sC%d\n", p, c);
		}
	}
}

void print(int c)
{
	if (c--) {
		print(c/26);
		putchar(c%26 + 'A');
	}
}

```cpp
```

[1]: http://en.cppreference.com/w/cpp/io/c/fscanf
[2]: https://gist.github.com/cjxgm/13ffd989f97e8e271be6

