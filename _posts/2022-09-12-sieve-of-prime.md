---
layout: post
title: "素数的线性筛法"
subtitle: "埃拉托斯特尼筛法以及线性筛法的原理"
date: 2022-09-12
author: "Luke Alpha"
header-img: "img/post-bg-2015.jpg"
mathjax: true
tags: 
    - 数学
---
## 埃拉托斯特尼筛法（埃氏筛）
一种筛素数的方法，复杂度$O(n\ln n)$

从小到大枚举每一个数字，如果之前被筛掉了，那么不是素数，否则就是素数。

无论当前的i是不是素数，枚举已经筛出来的每一个素数$pm[j]$，把$i\times pm[j]$筛掉。直到$i\times pm[j]$大于上界$n$或者素数枚举完毕时，算法结束。

## 线性筛法

埃氏筛慢在哪里呢？慢在一个数字可能被筛掉好多遍。

这时对于每一个数字x，仅在$pm[j’]\times i’$的时候被筛掉，其中$pm[j’]$是$x$最小的质因数。

在枚举pm的时候进行特判，如果$pm[j]\|i$，那说明$i$的最小质因子为$pm[j]$，之后再枚举$pm$就不满足要求了，直接退出循环。

### Code

```cpp
bool not_prime[MAXN];
int prm[MAXN];

void init(){
    memset(not_prime, 0, sizeof not_prime);
	for (int i = 2; i <= MAXQ;i ++){
		if(!not_prime[i]){
			prm[++cnt] = i;
		}
		for (int j = 1; j <= cnt && prm[j]*i <= MAXQ; j ++){
			not_prime[i*prm[j]] = true;
			if(i%prm[j] == 0) break;
		}
	}
}
```

