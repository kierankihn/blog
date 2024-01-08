---
title: 解题报告：P8355 Fence
date: 2023-10-30T15:47:02+08:00
tags:
  - 动态规划
  - 线性dp
  - 单调队列
categories:
  - 解题报告
---

## 题意

现在有 $n$ 个工人要粉刷一段篱笆，每个工人可以粉刷包含 $start_i$ 的一段最长不超过 $len_i$ 的篱笆（当然也可以不粉刷），每个工人粉刷长度为 $1$ 的篱笆的收益为 $cost_i$，求最大收益

<!-- more -->

## 思路

考虑 dp，设 $dp_{i, j}$ 表示前 $i$ 个人粉刷前 $j$ 段篱笆可以获得的最大收益

显然有如下的暴力转移
$$
\begin{align}
	dp_{i, j} & \leftarrow \min \lbrace dp_{i - 1, k} + (j - k) \times cost_i \rbrace, & \left(\text{让第 }i \text{ 个人粉刷区间 } (k, j\rbrack \right), \\
	dp_{i, j} & \leftarrow dp_{i - 1, j}, & \left(\text{第 } i \text{ 个人不粉刷}\right), \\
	dp_{i, j} & \leftarrow dp_{i, j - 1}, & \left(\text{第 } i \text{ 个人不粉刷}\right).
\end{align}
$$
观察发现，瓶颈在于如何快速求出 $\min \lbrace dp_{i - 1, k} + (j - k) \times cost_i \rbrace$

对上式进行变形，可得
$$
\begin{align}
\min \lbrace dp_{i - 1, k} + (j - k) \times cost_i \rbrace = & \min \lbrace dp_{i - 1, k} + j \times cost_i - k \times cost_i \rbrace \\
                                      = & \min \lbrace dp_{i - 1, k} - k \times  cost_i \rbrace + j \times cost_i
\end{align}
$$
即上式可以被分为两个部分，仅包含 $i, j$ 的和仅包含 $i, k$ 的部分，我们只需要求出 $\min \lbrace dp_{i - 1, k} - k \times  cost_i \rbrace$ 部分的最值即可

那么显然，在循环每个 $i$ 时，可以更新 $dp_{i, j}$ 的 $k$ 是在变化的，我们使用单调队列维护 $\min \lbrace dp_{i - 1, k} - k \times  cost_i \rbrace$，然后 $O(1)$ 更新即可

均摊时间复杂度为 $O(nm)$

## 代码

```cpp
#include <bits/stdc++.h>
namespace solution
{
    typedef long long LL;
    const int MAXN = 100 + 5;
    const int MAXM = 2e5 + 5;
    int n, m;
    struct Node
    {
        int start, len, cost;
    };
    bool operator<(const Node &a, const Node &b)
    {
        return a.start < b.start;
    }
    Node node[MAXN];
    int dp[MAXN][MAXM];
    int main()
    {
        scanf("%d%d", &m, &n);
        for (int i = 1; i <= n; i++)
        {
            scanf("%d%d%d", &node[i].len, &node[i].cost, &node[i].start);
        }
        std::sort(node + 1, node + n + 1);
        for (int i = 1; i <= n; i++)
        {
            std::deque<std::pair<int, int>> q;
            q.push_back(std::make_pair(0, 0));
            for (int j = 1; j <= m; j++)
            {
                dp[i][j] = std::max(dp[i - 1][j], dp[i][j - 1]);
                while (!q.empty() && q.front().second < j - node[i].len)
                {
                    q.pop_front();
                }
                if (!q.empty() && node[i].start <= j && j <= node[i].start + node[i].len - 1)
                {
                    dp[i][j] = std::max(dp[i][j], q.front().first + j * node[i].cost);
                }
                if (j < node[i].start)
                {
                    while (!q.empty() && q.back().first <= dp[i - 1][j] - node[i].cost * j)
                    {
                        q.pop_back();
                    }
                    q.push_back(std::make_pair(dp[i - 1][j] - node[i].cost * j, j));
                }
            }
        }
        printf("%d\n", dp[n][m]);
        return 0;
    }
}
int main()
{
    solution::main();
    return 0;
}
```

