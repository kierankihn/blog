---
title: 解题报告：P8350 Mondriaans Dream
date: 2023-10-27T18:29:32+08:00
tags:
  - 动态规划
  - 状态压缩
  - 状压dp
categories:
  - 解题报告
---
## 题意

给定一个 $n \times m$ 大小的长方形（$1 \le n, m \le 11$），求使用 $1 \times 2$ 大小的长方形铺满它有多少种方案

<!-- more -->

## 思路

显然本题可以使用状态压缩 dp 解决

设计状态 $dp_{i, j}$ 表示放到第 $i$ 行，且第 $i$ 行状态为 $j$ 的情况下的答案

$j$ 用二进制表示第 $k$ 个格子是否是竖着铺且靠上的那个格子

考虑转移

如果当前行状态 $p$ 与上一行状态 $q$ 取与运算结尾不为 $0$，那么显然 $p, q$ 无法转移，因为 $p, q$ 存在同一列的两个格子都是竖着铺且靠上的那个格子

同时，$p, q$ 取或运算的结果意味着在当前行是被 $1 \times 2$（即竖着放）的长方形覆盖的，那么剩余的格子就是被横着放的长方形覆盖的

由于是横着放的，那么其长度必定为偶数，否则就不合法

由此可以得到判断 $p, q$ 直接转移合不合法的代码：

```cpp
bool check(int x, int y)
{
    if ((x & y) != 0)
    {
        return false;
    }
    int tmp = (x | y) | (1 << m), last = -1, now = 0;
    while (tmp)
    {
        if ((tmp & 1) == 1)
        {
            if (((now - last - 1) & 1) != 0)
            {
                return false;
            }
            last = now;
        }
        tmp = tmp >> 1, now++;
    }
    return true;
}
```

注意在判断时要特别判断最后一段 $0$ 的长度是否为偶数，不能漏判

## 代码

```cpp
#pragma GCC optimize(3, "Ofast", "inline")
#include <bits/stdc++.h>
namespace solution
{
    typedef long long LL;
    const int MAXN = 12 + 5;
    const int MAX2N = (1 << 12) + 5;
    LL n, m;
    LL dp[MAXN][MAX2N];
    bool check(int x, int y)
    {
        if ((x & y) != 0)
        {
            return false;
        }
        int tmp = (x | y) | (1 << m), last = -1, now = 0;
        while (tmp)
        {
            if ((tmp & 1) == 1)
            {
                if (((now - last - 1) & 1) != 0)
                {
                    return false;
                }
                last = now;
            }
            tmp = tmp >> 1, now++;
        }
        return true;
    }
    int main()
    {
        while (scanf("%lld%lld", &n, &m) != EOF && n != 0 && m != 0)
        {
            std::memset(dp, 0, sizeof(dp));
            dp[0][0] = 1;
            for (int i = 1; i <= n; i++)
            {
                for (int j = 0; j < (1 << m); j++)
                {
                    for (int k = 0; k < (1 << m); k++)
                    {
                        if (check(j, k))
                        {
                            dp[i][j] += dp[i - 1][k];
                        }
                    }
                }
            }
            printf("%lld\n", dp[n][0]);
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

