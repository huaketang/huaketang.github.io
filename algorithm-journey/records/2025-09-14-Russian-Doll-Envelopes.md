## 刷题记录 — 俄罗斯套娃信封问题

### 1. 题目名称

[俄罗斯套娃信封问题 (LeetCode 354)](https://leetcode.cn/problems/russian-doll-envelopes/description/)

------

### 2. 题目描述

- 给你一个二维整数数组 `envelopes` ，其中 `envelopes[i] = [wi, hi]` ，表示第 `i` 个信封的宽度和高度。
   当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。
   请计算 **最多能有多少个** 信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。
- **注意**：不允许旋转信封。

------

### 3. 解题思路

#### (1) 为什么要排序？

- 目标：在二维数组中寻找最长递增子序列 (LIS)。
- 直接在二维上找 LIS 难度大。一个常见 trick：
  1. **先按照宽度升序排序**
  2. **当宽度相等时，高度按降序排序**
     - 这是为了避免 `[w, h]` 和 `[w, h2]`（相同宽度）同时被选入，因为宽度相等时不能套娃。降序保证 LIS 不会错误地把它们选上。

这样问题转化为：
 ➡️ 在排好序的信封数组里，只需要在 **高度数组** 上找 LIS。

#### (2) 二分搜索（扑克牌 LIS 贪心法）

- 维护一个 `top[]` 数组，表示当前所有“堆顶”的值。
- 对于每张新牌 `poker`：
  - 用二分查找找到 `top` 中第一个 `>= poker` 的位置，并替换；
  - 如果找不到（`poker` 比所有堆顶都大），则新建一个堆。

这样保证堆顶数组单调递增，其长度就是 LIS 长度。

#### (3) 二分搜索模板

- **闭区间写法**（常用）：

```java
int left = 0, right = piles - 1;
while (left <= right) {
    int mid = left + (right - left) / 2;
    if (top[mid] < poker) {
        left = mid + 1; // 去右边找
    } else {
        right = mid - 1; // 去左边找
    }
}
// 循环结束时，left 就是第一个 >= poker 的位置
```

➡️ 这是 **寻找第一个大于等于目标值 (`lower_bound`)** 的标准写法。

#### (4) 变种总结

- **第一个 >= target**：`if (a[mid] < target) left = mid + 1 else right = mid - 1`
- **第一个 > target**：`if (a[mid] <= target) left = mid + 1 else right = mid - 1`
- **最后一个 <= target**：`if (a[mid] <= target) left = mid + 1 else right = mid - 1`，最后答案是 `right`
- **最后一个 < target**：`if (a[mid] < target) left = mid + 1 else right = mid - 1`，最后答案是 `right`

------

### 4. Python 中的二分查找（bisect 模块）

Python 提供了 `bisect` 模块，可以替代手写二分查找。

常用函数：

- `bisect.bisect_left(a, x)`
   返回第一个 **>= x** 的位置（对应 `lower_bound`）。
- `bisect.bisect_right(a, x)`
   返回第一个 **> x** 的位置（对应 `upper_bound`）。

例子：

```python
import bisect

arr = [1, 3, 3, 5, 7]

print(bisect.bisect_left(arr, 3))   # 输出 1，第一个 >= 3 的位置
print(bisect.bisect_right(arr, 3))  # 输出 3，第一个 > 3 的位置
print(bisect.bisect_left(arr, 4))   # 输出 3，第一个 >= 4 的位置
```

在俄罗斯套娃问题里，Python 解法：

```python
import bisect

class Solution:
    def maxEnvelopes(self, envelopes):
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        height = [h for _, h in envelopes]

        top = []
        for h in height:
            idx = bisect.bisect_left(top, h)  # 找到第一个 >= h 的位置
            if idx == len(top):
                top.append(h)
            else:
                top[idx] = h
        return len(top)
```

------

### 5. Java 代码实现

```java
import java.util.Arrays;

class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        int length = envelopes.length;
        Arrays.sort(envelopes, (a, b) -> {
            if (a[0] == b[0]) return b[1] - a[1];
            return a[0] - b[0];
        });

        int[] height = new int[length];
        for (int i = 0; i < length; i++) {
            height[i] = envelopes[i][1];
        }
        return lengthOfLIS(height);
    }

    public int lengthOfLIS(int[] height) {
        int piles = 0, n = height.length;
        int[] top = new int[n];
        for (int poker : height) {
            int left = 0, right = piles - 1;
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (top[mid] < poker) {
                    left = mid + 1;
                } else {
                    right = mid - 1;
                }
            }
            if (left == piles) piles++;
            top[left] = poker;
        }
        return piles;
    }
}
```

------

### 6. 时间与空间复杂度

- **时间复杂度**：O(n log n)
  - 排序 O(n log n)，LIS O(n log n)
- **空间复杂度**：O(n)
  - `top` 数组最多 n 大小

------

### 7. 关键点总结

- 排序时宽度升序，高度降序，避免相同宽度时错误嵌套。
- 转化为 LIS 问题，核心是“扑克牌堆”思路。
- 二分查找常见的 `lower_bound` 和 `upper_bound` 写法要非常熟练。
- Python 可以用 `bisect` 模块，大大简化代码