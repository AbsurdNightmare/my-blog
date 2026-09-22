+++
date = '2026-09-21T16:54:09+08:00'
draft = false
title = 'Leetcode 面试150题——双指针 总结'
summary = '双指针部分题解汇总'
tags = ['Leetcode', '题解汇总', '双指针']
series = ['面试150题']
featured = false
math = true
+++

> 说在前：由于是双指针板块，所以采用的方法基本都是双指针。其他的方法基本跳过或者不考虑。

---

# 验证回文串
> 难度：简单

> 标签：双指针、字符串

> 链接：[验证回文串](https://leetcode.cn/problems/valid-palindrome/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个**回文串**。

字母和数字都属于字母数字字符。

给你一个字符串 s，如果它是**回文串**，返回`true`；否则，返回`false`。

{{< /tab >}}
{{< tab name="解法" >}}
1. **筛选 + 判断**。把字符串里面的数字字母筛出来，存入`str`，然后反转`str`对比是否相等。时间复杂度$O(len(s))$，空间复杂度是$O(len(s))$。
2. **双指针**。两个指针位于字符串两侧，每次都移动两边到一个数字字母，然后比较是不是一样的，不一样就返回。如果指针相遇就是回文串。时间复杂度$O(len(s))$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

{{< tabgroup >}}
{{< tab name="筛选 + 判断" >}}
```go
func isPalindrome(s string) bool {
    str := []rune{}
    for _, c := range s {
        if unicode.IsDigit(c) || unicode.IsLetter(c) {
            str = append(str, unicode.ToLower(c))
        }
    }
    rev := slices.Clone(str)  // slices包的极致运用
    slices.Reverse(rev)
    return slices.Equal(str, rev)
}
```

{{< /tab >}}
{{< tab name="双指针" >}}
```go
func isPalindrome(s string) bool {
    i, j := 0, len(s)-1
    for i < j {
        if !unicode.IsLetter(rune(s[i])) && !unicode.IsDigit(rune(s[i])) {
            i++
        } else if !unicode.IsLetter(rune(s[j])) && !unicode.IsDigit(rune(s[j])) {
            j--
        } else if unicode.ToLower(rune(s[i])) == unicode.ToLower(rune(s[j])) {
            i++
            j--
        } else {
            return false
        }
    }

    return true
}
```

---

# 判断子序列
> 难度：简单

> 标签：双指针、字符串、动态规划

> 链接：[判断子序列](https://leetcode.cn/problems/is-subsequence/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定字符串 s 和 t ，判断 s 是否为 t 的子序列。

字符串的一个子序列是原始字符串删除一些（也可以不删除）字符而不改变剩余字符相对位置形成的新字符串。（例如，"ace"是"abcde"的一个子序列，而"aec"不是）。

{{< /tab >}}
{{< tab name="解法" >}}
**双指针**

两个指针都从头开始匹配。如果都对就都移动，如果不匹配移动 t 串的指针。时间复杂度$O(len(s))$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func isSubsequence(s string, t string) bool {
    i, j := 0, 0
    for (i < len(s) && j < len(t)) {
        if (s[i] != t[j]) {
            j++
        } else {
            i++
            j++
        }
    }

    if (i == len(s)) {
        return true
    }
    return false
}
```

---

# 两数之和 II - 输入有序数组
> 难度：中等

> 标签：双指针、字符串、二分查找

> 链接：[两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个下标从 1 开始的整数数组`numbers`，该数组已按**非递减顺序排列**。

请你从数组中找出满足相加之和等于目标数`target`的**两个**数。令这两个数分别是`numbers[index1]`和`numbers[index2]`，其中`1 <= index1 < index2 <= numbers.length`。

以长度为 2 的整数数组`[index1, index2]`的形式返回这两个整数的下标`index1`和`index2`。

你可以假设每个输入**只对应唯一的答案**，而且你**不可以**重复使用相同的元素。

你所设计的解决方案必须只使用常数级的额外空间。

{{< /tab >}}
{{< tab name="解法" >}}
**双指针**

因为是有序的，所以直接两个指针位于两侧，和小了左边往右移动，大了右边往左移动。时间复杂度$O(len(s))$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func twoSum(numbers []int, target int) []int {
    i, j := 0, len(numbers)-1
    for {
        s := numbers[i] + numbers[j]
        if s == target {
            return []int{i+1, j+1}
        }
        if s > target {
            j--
        } else {
            i++
        }
    }
}
```

---

# 盛最多水的容器
> 难度：中等

> 标签：数组、贪心、双指针

> 链接：[盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个长度为 n 的整数数组`height`。有 n 条垂线，第 i 条线的两个端点是`(i, 0)`和`(i, height[i])`。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。

{{< /tab >}}
{{< tab name="解法" >}}
**双指针**

盛水有经典的“短板效应”。我们从最远的两端开始，此时水容量是`minHeight * width`。此时如果我们向内移动较短的板，板可能变长导致乘积变大，也可能更短了导致乘积变小。但是如果向内移动长板，由于受到短板的牵制，面积是一定变小的（因为宽也变小了）。所以我们一直移动短板，记录每次的最大面积，直到指针相遇。时间复杂度$O(n)$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func maxArea(height []int) (ans int) {
    n := len(height)
    i, j := 0, n-1
    for i < j {
        ans = max(ans, min(height[i], height[j])*(j-i))
        if height[i] <= height[j] {
            i++
        } else {
            j--
        }
    }
    return
}
```

---

# 三数之和
> 难度：中等

> 标签：数组、贪心、排序

> 链接：[三数之和](https://leetcode.cn/problems/3sum/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个整数数组`nums`，判断是否存在三元组`[nums[i], nums[j], nums[k]]`满足`i != j、i != k 且 j != k`，同时还满足`nums[i] + nums[j] + nums[k] == 0`。请你返回所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。

{{< /tab >}}
{{< tab name="解法" >}}
**双指针**

上上一道题的升级版。我们先排序，然后只要每次固定一个值，就变成两数之和的问题，又是双指针。唯一注意的就是不能包含重复的数组，所以如果`nums[i-1] == nums[i]`，那就会出现重复的情况。包括`nums[j] == nums[j-1]`的时候也会出现重复。所以要遇到这种情况就跳过。时间复杂度$O(n^2)$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func threeSum(nums []int) (ans [][]int) {
    slices.Sort(nums)
    n := len(nums)
    for i, x := range nums {
        if i > 0 && nums[i-1] == x {
            continue
        }

        j, k := i+1, n-1
        for j < k {
            sum := x+ nums[j] + nums[k]
            if sum == 0 {
                if j == i+1 || nums[j] != nums[j-1] {
                    ans = append(ans, []int{x, nums[j], nums[k]})
                }
                j++
                k--
            } else if sum < 0 {
                j++
            } else {
                k--
            }
        }
    }
    return
}
```