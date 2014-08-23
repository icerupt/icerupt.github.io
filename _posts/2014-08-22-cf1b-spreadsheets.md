---
layout: post
title: "[cf1b] Spreadsheets"
description: ""
category: "codeforces"
tags: ["字符串", "模拟", "codeforces"]
---
{% include JB/setup %}

### 题意
将题意中描述的两种定位表格的方式相互转化。一种是 StrX, 另一种是 RXCY。
其中 X 表示行号，Y 表示列号，Str 是 Y 的字母串表示，A..Z 分别表示 1..26
（没有 0），可以看成 26 进制。

### 题解
模拟就好。读入的时候可以用 [std::sscanf][1] 实现匹配。
[cjxgm用c++11的正则库实现][2], codeforces 似乎不支持，仅供围观。

### 实现
```cpp
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

```

[1]: http://man7.org/linux/man-pages/man3/scanf.3.html
[2]: https://gist.github.com/cjxgm/13ffd989f97e8e271be6

