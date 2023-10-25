---
title: 解题报告：P8336 LCIS 最长公共上升子序列
date: 2023-10-25 14:49:48
tags: 动态规划, 线性dp
---

## 题意

给定两个字符串 $a, b$，求 $a, b$ 的最长公共上升子序列

## 暴力

参考最长公共子序列的做法，设计 dp 状态为 $dp_{i, j}$ 表示匹配到 $a_i$ 时以 $b_j$ 结尾的最长公共上升子序列

考虑转移：

- 当 $a_i \not = b_j$ 时，显然有 $dp_{i, j} = dp_{i - 1, j}$
- 当 $a_i = b_j$ 时，显然有 $dp_{i, j} = \min \limits_{1 \le k < j, \  b_k < b_j} (dp_{i - 1, k})$

暴力转移的时间复杂度为 $O(n^3)$

考虑优化 $a_i = b_j$ 时的转移

因为 $a_i = b_j$，上式显然等价于 $dp_{i, j} = \min \limits_{1 \le k < j, \  b_k < a_i} (dp_{i - 1, k})$

那么对于每一个 $i$ ，转移时所需要的决策集合是只增不减的，我们可以在转移的同时维护决策集合（中的最优方案），这样转移只需 $O(1)$ 判断即可

可以发现 $dp_{i, j}$ 只会从 $k < j$ 的地方转移过来，所以我们还可以使用滚动数组优化

那么最后的转移方程即为

$$dp_{j} = \min(dp_{j}, val + 1), \ val = \min\limits_{1 \le k < j,\ b_k < a_i}(dp_k)$$

## 代码

```cpp
#include <bits/stdc++.h>
namespace solution
{
    typedef long long LL;
    const int MAXN = 3000 + 5;
    int n;
    int ans = 0;
    int max[MAXN];
    int dp[MAXN];
    int s1[MAXN], s2[MAXN];
    int main()
    {
        scanf("%d", &n);
        for (int i = 1; i <= n; i++)
        {
            scanf("%d", &s1[i]);
        }
        for (int i = 1; i <= n; i++)
        {
            scanf("%d", &s2[i]);
        }
        for (int i = 1; i <= n; i++)
        {
            int val = 0;
            for (int j = 1; j <= n; j++)
            {
                if (s1[i] == s2[j])
                {
                    dp[j] = std::max(dp[j], val + 1);
                }
                if (s2[j] < s1[i])
                {
                    val = std::max(val, dp[j]);
                }
                ans = std::max(ans, dp[j]);
            }
        }
        printf("%d\n", ans);
        return 0;
    }
}
int main()
{
    solution::main();
    return 0;
}
```

