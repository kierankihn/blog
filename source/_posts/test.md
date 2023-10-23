---
title: test
date: 2023-10-23 14:21:10
---

### 题意简述

给定 $n$ 个人，以及其生命值 $a_i$，这 $n$ 个人之间的关系构成一个环，每个人死亡时会对下一个人造成 $b_i$ 的伤害，你可以选择任意数量的人造成任意伤害，求为了使所有人死亡你所需要造成的伤害

### 思路


### 代码

```cpp
#include <bits/stdc++.h>
namespace solution
{
    typedef long long LL;
    const int MAXN = 3e5 + 5;
    const LL INF = 0x3f3f3f3f3f3f3f3fLL;
    int n;
    LL sum = 0;
    LL ans = INF;
    LL a[MAXN], b[MAXN], c[MAXN];
    int main()
    {
        freopen("doge.in", "r", stdin);
        freopen("doge.out", "w", stdout);
        scanf("%d", &n);
        for (int i = 1; i <= n; i++)
        {
            scanf("%lld%lld", &a[i], &b[i]);
        }
        for (int i = 1; i <= n; i++)
        {
            c[i % n + 1] = std::max(0LL, a[i % n + 1] - b[i]);
            sum += c[i % n + 1];
        }
        for (int i = 1; i <= n; i++)
        {
            ans = std::min(ans, sum - c[i] + a[i]);
        }
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