# 动态规划（Dynamic Programming）

动态规划是面试和竞赛中最常考的算法类型之一。它通过将复杂问题拆分为相互重叠的子问题，并存储子问题的解来避免重复计算，从而大幅提升算法效率。

---

## 核心思想

动态规划的核心在于两个关键特性：

1. **最优子结构**：一个问题的最优解可以由其子问题的最优解构造得到
2. **重叠子问题**：在递归过程中，同一个子问题会被多次求解

动态规划的本质是**以空间换时间**，通过记忆化（Memoization）或自底向上（Tabulation）的方式，将指数级时间复杂度降为多项式级。

---

## 解题框架

### 第一步：确定状态

状态是描述问题所需的最小信息。找状态的核心是回答：**最后一步是什么？**

以经典问题「爬楼梯」为例：
- `dp[i]` = 到达第 `i` 阶楼梯的方法数
- 这里的状态就是「当前在第几阶」

### 第二步：推导转移方程

根据问题的逻辑，推导出状态之间的关系：

```typescript
// 爬楼梯
dp[i] = dp[i-1] + dp[i-2]
```

### 第三步：确定边界和初始值

```typescript
dp[0] = 1  // 到达第0阶有一种方式（不动）
dp[1] = 1  // 到达第1阶有一种方式（走1步）
```

### 第四步：确定计算顺序

- **自顶向下**（记忆化递归）：从大往小算
- **自底向上**（DP迭代）：从小往大算

---

## 经典问题分类

### 一维 DP

#### 1. 爬楼梯（Climbing Stairs）

```typescript
function climbStairs(n: number): number {
    if (n <= 2) return n;
    
    let prev1 = 1, prev2 = 1;
    for (let i = 2; i <= n; i++) {
        const curr = prev1 + prev2;
        prev1 = prev2;
        prev2 = curr;
    }
    return prev2;
}
```

**复杂度**：时间 O(n)，空间 O(1)

---

#### 2. 打家劫舍（House Robber）

```typescript
function rob(nums: number[]): number {
    if (nums.length === 1) return nums[0];
    
    let prev2 = 0, prev1 = 0;
    for (const num of nums) {
        const curr = Math.max(prev1, prev2 + num);
        prev2 = prev1;
        prev1 = curr;
    }
    return prev1;
}
```

**核心思想**：对于每间房屋，选择「抢」或「不抢」，取最大值。

---

#### 3. 最长递增子序列（LIS）

```typescript
function lengthOfLIS(nums: number[]): number {
    const n = nums.length;
    const dp = new Array(n).fill(1);
    
    for (let i = 1; i < n; i++) {
        for (let j = 0; j < i; j++) {
            if (nums[j] < nums[i]) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }
    
    return Math.max(...dp);
}
```

**进阶优化**：使用二分查找 + patience sorting 可优化至 O(n log n)。

---

### 二维 DP

#### 4. 不同路径（Unique Paths）

```typescript
function uniquePaths(m: number, n: number): number {
    const dp = Array.from({ length: m }, () => 
        Array(n).fill(1)
    );
    
    for (let i = 1; i < m; i++) {
        for (let j = 1; j < n; j++) {
            dp[i][j] = dp[i-1][j] + dp[i][j-1];
        }
    }
    
    return dp[m-1][n-1];
}
```

---

#### 5. 最长公共子序列（LCS）

```typescript
function longestCommonSubsequence(text1: string, text2: string): number {
    const m = text1.length, n = text2.length;
    const dp = Array.from({ length: m + 1 }, () => 
        Array(n + 1).fill(0)
    );
    
    for (let i = 1; i <= m; i++) {
        for (let j = 1; j <= n; j++) {
            if (text1[i-1] === text2[j-1]) {
                dp[i][j] = dp[i-1][j-1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    
    return dp[m][n];
}
```

---

### 背包问题

#### 6. 0-1 背包

```typescript
function knapsack(weights: number[], values: number[], capacity: number): number {
    const n = weights.length;
    const dp = Array.from({ length: n + 1 }, () => 
        Array(capacity + 1).fill(0)
    );
    
    for (let i = 1; i <= n; i++) {
        for (let j = 0; j <= capacity; j++) {
            dp[i][j] = dp[i-1][j];
            if (j >= weights[i-1]) {
                dp[i][j] = Math.max(
                    dp[i][j],
                    dp[i-1][j - weights[i-1]] + values[i-1]
                );
            }
        }
    }
    
    return dp[n][capacity];
}
```

---

#### 7. 完全背包

```typescript
function completeKnapsack(weights: number[], values: number[], capacity: number): number {
    const n = weights.length;
    const dp = Array(capacity + 1).fill(0);
    
    for (let i = 0; i < n; i++) {
        for (let j = weights[i]; j <= capacity; j++) {
            dp[j] = Math.max(dp[j], dp[j - weights[i]] + values[i]);
        }
    }
    
    return dp[capacity];
}
```

**0-1 背包与完全背包的区别**：
- 0-1 背包：每件物品只能选一次，内层循环**逆序**
- 完全背包：每件物品可无限选，内层循环**顺序**

---

### 区间 DP

#### 8. 戳气球

```typescript
function maxCoins(nums: number[]): number {
    const n = nums.length;
    const arr = [1, ...nums, 1];
    const dp = Array.from({ length: n + 2 }, () => 
        Array(n + 2).fill(0)
    );
    
    for (let len = 2; len <= n + 1; len++) {
        for (let left = 0; left + len <= n + 1; left++) {
            const right = left + len;
            for (let k = left + 1; k < right; k++) {
                dp[left][right] = Math.max(
                    dp[left][right],
                    dp[left][k] + dp[k][right] + arr[left] * arr[k] * arr[right]
                );
            }
        }
    }
    
    return dp[0][n + 1];
}
```

---

### 状态压缩 DP

#### 9. 旅行商问题（TSP）

```typescript
function travelingSalesman(dist: number[][]): number {
    const n = dist.length;
    const END = (1 << n) - 1;
    const dp = Array.from({ length: n }, () => 
        Array(1 << n).fill(Infinity)
    );
    
    for (let i = 0; i < n; i++) {
        dp[i][1 << i] = 0;
    }
    
    for (let mask = 1; mask <= END; mask++) {
        for (let u = 0; u < n; u++) {
            if (!(mask & (1 << u))) continue;
            for (let v = 0; v < n; v++) {
                if (mask & (1 << v)) continue;
                const nextMask = mask | (1 << v);
                dp[v][nextMask] = Math.min(
                    dp[v][nextMask],
                    dp[u][mask] + dist[u][v]
                );
            }
        }
    }
    
    let ans = Infinity;
    for (let i = 0; i < n; i++) {
        ans = Math.min(ans, dp[i][END]);
    }
    return ans;
}
```

---

## 常见优化技巧

### 1. 空间优化

**滚动数组**：当 DP 只依赖前一行时，可将二维数组压缩为一维。

```typescript
// 优化前
dp[i][j] = dp[i-1][j] + dp[i][j-1];

// 优化后（一维）
dp[j] = dp[j] + dp[j-1];
```

### 2. 时间优化

**单调队列优化**：适用于状态转移涉及区间最值的情况。

**斜率优化**：适用于状态转移方程可化为线性规划形式的情况。

### 3. 初始化技巧

- **虚拟节点**：在数组两端添加哨兵节点，简化边界处理
- **多起点**：根据题意设置多个初始状态

---

## 面试高频模板总结

| 问题类型 | 核心模板 | 关键点 |
|---------|---------|-------|
| 线性DP | `dp[i]` 依赖前几个状态 | 找状态转移方程 |
| 背包问题 | 选或不选、选多少 | 内层循环方向 |
| 区间DP | `dp[l][r]` 枚举分割点 | 三重循环枚举长度 |
| 状态压缩 | 位运算枚举子集 | `mask` 表示已选集合 |
| 树形DP | DFS + DP | 父子节点状态转移 |

---

## 实战建议

1. **先想暴力，再优化**：先用递归+记忆化明确思路，再考虑迭代优化
2. **画状态转移图**：帮助理清状态之间的依赖关系
3. **从简单例子推导**：手写小数据的 DP 表格
4. **注意边界条件**：数组下标越界是常见错误
5. **空间复杂度分析**：能否用滚动数组进一步优化？

---

> 💡 **提示**：动态规划的核心是多做多练。建议从 Easy 题目开始，理解每种类型问题的套路后，再挑战 Medium/Hard 题目。
