---
title: 解题报告：P6064 [USACO05JAN] Naptime G
date: 2023-10-29T09:05:26+08:00
tags:
  - 动态规划
  - 线性dp
  - 断环
---
## 题意

洛谷：[link](https://www.luogu.com.cn/problem/P6064)

贝茜是一只非常缺觉的奶牛．她的一天被平均分割成 $N$ 段（$3 \leq N \leq 3830$），但是她要用其中的 $B$ 段时间（$2 \leq B \lt N$）睡觉。每段时间都有一个效用值 $U_i$（$0 \leq U_i \leq 2 \times 10^5$），只有当她睡觉的时候，才会发挥效用。

有了闹钟的帮助，贝茜可以选择任意的时间入睡，当然，她只能在时间划分的边界处入睡、醒来。

贝茜想使所有睡觉效用的总和最大。不幸的是，每一段睡眠的第一个时间阶段都是“入睡”阶段，而旦不记入效用值。

时间阶段是不断循环的圆（一天一天是循环的嘛），假如贝茜在时间 $N$ 和时间 $1$ 睡觉，那么她将得到时间 $1$ 的效用值。

## 思路

首先考虑如果时间不循环的做法

显然可以设计 dp，令 $dp_{i, j, 0/1}$ 表示当前选到第 $i$ 天，已经睡了 $j$ 天，当前天睡了 / 没睡情况下的最大值

可以有如下转移
$$
dp_{i, j, 0} = & \min \left \lbrace dp_{i - 1, j, 0}, \ dp_{i - 1, j, 1} \right \rbrace \newline
dp_{i, j, 1} = & \min \left \lbrace dp_{i - 1, j, 0}, \ dp_{i - 1, j, 1} + v_i \right \rbrace
$$
初始状态为 $dp_{1, 0, 0} = dp_{1, 1, 1} = 0$, 其余均为 $-\infin$

考虑如何断环，第一个想法便是将原数组复制一遍到原数组后边，这样即可求出不再同一天睡觉的情况

但这样真的正确吗？

这样做可能会导致一个问题：在原数组前边和后边选了同一段

例如在 $[1, n]$ 区间中选了时段 $x$，在 $[n + 1, 2n]$ 区间中又选了一次时段 $x$，这样会导致同一时间被选了多次，正确性有问题

那么如何解决呢？

考虑分类讨论，第 $n$ 天如果睡觉的话，那么就可以得到第一天睡觉的收益，否则就无法得到

我们可以钦定第 $n$ 天睡觉，同时将初始状态修改为 $dp_{1, 0, 0} = dp_{1, 1, 1} = v_1$，求出答案，然后再不钦定第 $n$ 天睡觉，求一遍答案，两次求得的答案取 $\max$ 即可

## 代码

```cpp
#include <bits/stdc++.h>
namespace solution
{
    typedef long long LL;
    const int MAXN = 4000 + 5;
    int n, m;
    LL ans = 0;
    LL v[MAXN];
    LL dp[MAXN][2];
    void solve()
    {
        for (int i = 2; i <= n; i++)
        {
            for (int j = m; j >= 1; j--)
            {
                dp[j][0] = std::max(dp[j][0], dp[j][1]);
                dp[j][1] = std::max(dp[j - 1][0], dp[j - 1][1] + v[i]);
            }
        }
    }
    int main()
    {
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; i++)
        {
            scanf("%lld", &v[i]);
        }

        std::memset(dp, 0xcf, sizeof(dp));
        dp[0][0] = 0, dp[1][1] = 0;
        solve();
        ans = std::max({ans, dp[m][0], dp[m][1]});

        std::memset(dp, 0xcf, sizeof(dp));
        dp[0][0] = 0, dp[1][1] = v[1];
        solve();
        ans = std::max({ans, dp[m][1]});

        printf("%lld\n", ans);
        return 0;
    }
}
int main()
{
    solution::main();
    return 0;
}
```



