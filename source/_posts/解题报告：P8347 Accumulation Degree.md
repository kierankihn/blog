---
title: 解题报告：P8347 Accumulation Degree
date: 2023-10-26T16:21:49+08:00
tags:
  - 树状dp
  - 动态规划
  - 换根dp
categories:
  - 解题报告
---
## 题意

给定一棵 $n$ 个节点的无根树（$n \le 2 \times 10 ^ 5$），每一条树上的边有一个流量，你需要选取一个点为根（源点），所有叶子节点为汇点，使得树中的最大流量最大（到达所有汇点的流量之和最大）

<!-- more -->

## 思路

由于本题没有指定树根，可以考虑换根 dp

首先假设节点 1 为根，求出每个节点到其子树的最大流量 $d_i$（当 $i$ 无儿子时 $d_i = 0$），记以 $u$ 为根时的最大流量为 $dp_u$，那么显然有 $dp_1 = d_1$

考虑转移

假设我们已经求出了 $dp_{u}$ ，且 $u$ 存在一个儿子 $v$，那么当以 $v$ 为根时其流量会分为两部分，一部分是 $v$ 到其子树的流量，一部分是 $v$ 通过 $v$ 到 $u$ 的边流向其他地方的流量

那么显然 $v$ 到其子树的流量为 $d_v$，而 $v$ 通过 $v$ 到 $u$ 的边流向其他地方的流量为 $dp_u$ 减去 $v$ 对 $dp_u$ 做出的贡献

此处要注意 $u$ 的度数为 $1$ 的情况，$v$ 的度数为 $1$ 的情况并分类讨论

则有如下转移方程

$$
dp_v = d_v + 
\begin{cases}
	c(u, v), & (du_u = 1) \\
	\min(c(u, v), dp_u - \min(d_v, c(u, v))), & (du_u \not = 1)
\end{cases}
$$

其中，$du_i$ 表示节点 $i$ 的度数

## 代码

```cpp
#include <bits/stdc++.h>
namespace solution
{
    typedef long long LL;
    const int MAXN = 2e5 + 5;
    const int INF = 0x3f3f3f3f;
    int t;
    int n;
    int ans = 0;
    struct Edge
    {
        int from, to, cap;
        Edge(int _from, int _to, int _cap) : from(_from), to(_to), cap(_cap) {}
    };
    std::vector<Edge> edge;
    std::vector<int> g[MAXN];
    void insert(int u, int v, int c)
    {
        g[u].push_back(edge.size());
        edge.push_back(Edge(u, v, c));
    }
    int d[MAXN], dp[MAXN];
    int dfs(int u, int fa)
    {
        for (auto &i : g[u])
        {
            auto &e = edge[i];
            if (e.to == fa)
            {
                continue;
            }
            d[u] += std::min(dfs(e.to, u) == 0 ? INF : d[e.to], e.cap);
        }
        return d[u];
    }
    void solve(int u, int fa)
    {
        ans = std::max(ans, dp[u]);
        for (auto &i : g[u])
        {
            auto &e = edge[i];
            if (e.to == fa)
            {
                continue;
            }
            dp[e.to] = d[e.to] + std::min(e.cap, dp[u] - std::min((d[e.to] == 0 ? INF : d[e.to]), e.cap));
            solve(e.to, u);
        }
    }
    void init()
    {
        ans = 0;
        edge.clear();
        for (int u = 1; u <= n; u++)
        {
            dp[u] = d[u] = 0, g[u].clear();
        }
    }
    int main()
    {
        scanf("%d", &t);
        while (t--)
        {
            init();
            scanf("%d", &n);
            for (int i = 1; i < n; i++)
            {
                int u, v, c;
                scanf("%d %d %d", &u, &v, &c);
                insert(u, v, c), insert(v, u, c);
            }
            dp[1] = dfs(1, 0), solve(1, 0);
            printf("%d\n", ans);
        }
        return 0;
    }
}
int main()
{
    solution::main();
    return 0;
}
```
