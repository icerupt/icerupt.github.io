---
layout: post
title: "[poj2454] Jersey Politics"
description: ""
category: "poj"
tags: ["动态规划", "随机化", "有待深入研究"]
---
{% include JB/setup %}

### 题意
将\\(3 k\\)个城市划分成\\(3\\)个大小为\\(k\\)的区域，已知每个城市有总数为\\(1000\\)的 Jersey Cow 和 Wisconsin Cow，
要求输出一种方案使得至少两个区域， Jersey Cow的数目严格大于 Wisconsin Cow。


### 题解
可以先排个序，对于前\\(2k\\)个城市做一个求个数为\\(k\\)的最小差值，
但是输出方案写起来比较恶心，因为一开始忘了个数限制就写了。。好吧，
不想改就用的随机化瞎搞搞过了，效率还挺高。动规什么的有心情再写吧，
估计是不会有了。。。

### 实现
```cpp
#include <iostream>
#include <algorithm>
#include <vector>
#include <ctime>
using namespace std;

struct data { int da, p; };
struct cmpclass { bool operator()(data a, data b) { return a.da > b.da; } } cmp;

vector<data> da;
typedef vector<data>::iterator data_it;
int k;

int main()
{
	ios::sync_with_stdio(false);
	cin >> k;
	da.resize(3*k);
	for (int i = 0; i < 3*k; i++) { cin >> da[i].da; da[i].p = i+1; }
	sort(da.begin(), da.end(), cmp);
	int s1 = 0, s2 = 0;
	for (int i = 0; i < k; i++) s1 += da[i].da;
	for (int i = k; i < 2*k; i++) s2 += da[i].da;
	while (s1 <= k*500 || s2 <= k*500) {
		int t1 = rand()%k, t2 = rand()%k+k;
		s1 -= (da[t1].da - da[t2].da);
		s2 += (da[t1].da - da[t2].da);
		swap(da[t1], da[t2]);
	}
	for (data_it it = da.begin(); it != da.end(); ++it)
		cout << (*it).p << "\n";
}

```

