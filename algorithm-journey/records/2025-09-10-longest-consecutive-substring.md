## 刷题记录 — 最长连续子字符串

### 1. 题目名称

最长连续子字符串 (Longest Consecutive Substring)

------

### 2. 题目描述

给定一个字符串，找到其中最长的由相同字符组成的连续子串，并返回其长度。
 例如：

- 输入：`"abbcccddddeeeeedcba"`
- 输出：`5` （因为 `"eeeee"` 最长）

------

### 3. 解题思路

1. **滑动窗口 / 双指针**
   - 用两个指针遍历字符串：`left` 指向当前连续子串的起点，`right` 向右移动。
   - 当遇到不同字符时，更新最长长度并重置 `left`。
2. **单次遍历统计**
   - 用一个变量记录当前连续字符的长度 `count`，遇到相同字符时 +1，遇到不同字符时重置为 1。
   - 过程中维护一个最大值 `maxLen`。

------

### 4. 关键点总结

- 字符串连续性 → 不需要额外数据结构，**一次遍历 O(n)** 即可。
- 注意边界情况：字符串为空、全相同字符、只有一个字符。

------

### 5. 代码实现（Java 示例）

```
public class LongestSubstring {
    public int longestSubstring(String s) {
        if (s == null || s.length() == 0) return 0;
        int maxLen = 1, count = 1;
        for (int i = 1; i < s.length(); i++) {
            if (s.charAt(i) == s.charAt(i - 1)) {
                count++;
            } else {
                count = 1;
            }
            maxLen = Math.max(maxLen, count);
        }
        return maxLen;
    }

    public static void main(String[] args) {
        LongestSubstring solver = new LongestSubstring();
        System.out.println(solver.longestSubstring("abbcccddddeeeeedcba")); // 5
    }
}
```

------

### 6. 时间与空间复杂度

- **时间复杂度**：O(n) （单次遍历）
- **空间复杂度**：O(1) （只用常数变量）

------

### 7. 复盘与反思

- 这道题属于 **基础字符串遍历题**，考察连续统计和边界处理。
- 易错点：
  - 忘记初始化 `maxLen = 1`（导致空串情况出错）。
  - 没有在循环结束后更新最大值。

------

### 8. 扩展与变形

- 统计最长连续子串的 **起始和结束下标**。
- 统计所有最长连续子串（可能有多个）。
- 类似题：
  - **最长连续数字**
  - **最长连续递增子序列**