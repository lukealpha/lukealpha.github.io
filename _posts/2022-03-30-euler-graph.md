---
layout: post
title: "欧拉回路及其相关问题"
subtitle: "基本定理、和DFS求解"
date: 2022-03-30
author: "Luke Alpha"
header-img: "img/post-bg-2015.jpg"
mathjax: true
tags: 
    - 图论
---
## 定理及推论[^footnote1]

### 无向图G存在欧拉通路的充要条件是：
G为连通图，并且G仅有两个奇度结点（度数为奇数的顶点）或者无奇度结点。

#### 推论1
1. 当G是仅有两个奇度结点的连通图时，G的欧拉通路必以此两个结点为端点。
2. G为欧拉图（存在欧拉回路）的充分必要条件是G为无奇度结点的连通图。


### 有向图D存在欧拉通路的充要条件是：

D为有向图，D的基图（即有向图去掉方向）连通，并且所有顶点的出度与入度都相等；或者除两个顶点外，其余顶点的出度与入度都相等，而这两个顶点中一个顶点的出度与入度之差为1，另一个顶点的出度与入度之差为-1。

#### 推论2：
1. 当D除出、入度之差为1，-1的两个顶点之外，其余顶点的出度与入度都相等时，D的有向欧拉通路必以出、入度之差为1的顶点作为始点，以出、入度之差为-1的顶点作为终点。
2. 有向图D为有向欧拉图的充分必要条件是D的基图为连通图，并且所有顶点的出、入度都相等。

## 欧拉回路的求解

### DFS求解
以[「一本通 3.7 例 1」欧拉回路 - LibreOJ](https://loj.ac/p/10105)为例:

>**题目描述**
>
>有一天一位灵魂画师画了一张图，现在要你找出欧拉回路，即在图中找一个环使得每条边都在环上出现恰好一次。
>一共两个子任务：
>1. 这张图是无向图。（$50$分）
>2. 这张图是有向图。（$50$分）

假设已经确定图G为一个欧拉图，那么我们设C1、C2是图的两个没有公共边，但有至少一个公共顶点的简单回路，我们可以将它们合并成一个新的简单回路C'。

此时我们只需要按照如下方式合并
<img src="/img/2022-03-30_euler_graph/combination.png" style="zoom:50%" />

假定Ans序列为欧拉回路遍历顺序。

我们可以想象欧拉图的某两个部分即为两个环。先进行DFS遍历，直到无法再继续遍历。此时进行回溯。回溯过程中发现路上还有一个环，此时将这个环插入到当前Ans序列（也是一个环）的中间，并不会破坏原有的序列。Ans序列将形成一个更大的回路。

以下是DFS求解欧拉回路(上述例题)的代码:
```cpp
#include <cstdio>
#include <iostream>
#include <algorithm>
#include <cstring>
#define MAXN 100010
#define MAXM 400010
using namespace std;

int head[MAXN], to[MAXM], nxt[MAXM], cnt = 1;
void add_edge(int u, int v) {
    to[++cnt] = v;
    nxt[cnt] = head[u];
    head[u] = cnt;
    return ;
}
bool flag[MAXM];
int outnode[MAXN], innode[MAXN], ans[MAXN], tt;
int type, n, m;
void dfs(int x) {
    for (int &i = head[x]; i; i = nxt[i]) {
        int y = to[i];
        int c = (type == 1 ? i / 2 : i - 1);
        int sig = i % 2;

        if (flag[c])
            continue;

        flag[c] = 1;
        dfs(y);

        if (type == 1)
            ans[++tt] = (sig ? -c : c);
        else
            ans[++tt] = c;
    }
}

int main() {
    scanf("%d", &type);
    scanf("%d%d", &n, &m);

    for (int i = 1; i <= m; i ++) {
        int x, y;
        scanf("%d%d", &x, &y);
        add_edge(x, y);

        if (type == 1) {
            add_edge(y, x);
        }

        ++ outnode[x];
        ++ innode[y];
    }

    if (type == 1) {
        for (int i = 1; i <= n; i ++) {
            if ((innode[i] + outnode[i]) % 2) {
                printf("NO");
                return 0;
            }
        }
    } else {
        for (int i = 1; i <= n; i ++) {
            if (innode[i] != outnode[i]) {
                printf("NO");
                return 0;
            }
        }
    }

    for (int i = 1; i <= n; i ++) {
        if (head[i]) {
            dfs(i);
            break;
        }
    }
    if (tt != m) {
        printf("NO");
        return 0;
    }
    printf("YES\n");

    for (int i = m; i >= 1; i --) {
        printf("%d ", ans[i]);
    }

    return 0;
}
```

## 引用和参考
[^footnote1]:参考[https://www.cnblogs.com/wkfvawl/p/9626163.html](https://www.cnblogs.com/wkfvawl/p/9626163.html)

