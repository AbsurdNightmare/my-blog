+++
date = '2026-09-21T16:54:09+08:00'
draft = false
title = 'Leetcode 面试150题——滑动窗口 总结'
summary = '滑动窗口部分题解汇总'
tags = ['Leetcode', '题解汇总', '滑动窗口']
series = ['面试150题']
featured = false
math = true
+++

> 说在前：由于是滑动窗口板块，所以采用的方法基本都是滑动窗口。其他的方法基本跳过或者不考虑。

# 长度最小的子数组
> 难度：中等

> 标签：数组、二分查找、前缀和、滑动窗口

> 链接：[长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个含有 n 个正整数的数组和一个正整数`target`。

找出该数组中满足其总和大于等于`target`的长度最小的**子数组**`[numsl, numsl+1, ..., numsr-1, numsr]`，并返回其长度。如果不存在符合条件的子数组，返回 0 。

{{< /tab >}}
{{< tab name="解法" >}}
**滑动窗口**

以这道题为例，介绍滑动窗口的基本思路：
1. 从头开始。本质还是基于双指针的技巧，左右指针都从头开始遍历。
2. 扩大窗口。右指针移动，每次都把新的数加入窗口，做题干需要的判断，如果符合条件了，记录窗口长度`right - left + 1`。
3. 缩短窗口。记录长度之后，移动左指针，把最左边的数移出窗口，做判断直到不符合条件，回到第二步继续。

时间复杂度$O(n)$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码:

```go
func minSubArrayLen(target int, nums []int) int {
    left, sum := 0, 0
    ans := len(nums)+1

    for right, num := range nums {
        sum += num
        for sum >= target {
            ans = min(ans, right-left+1)
            sum -= nums[left]
            left++
        }
    }

    if (ans > len(nums)) {
        return 0
    }
    return ans
}
```
---

# 无重复字符的最长子串
> 难度：中等

> 标签：数组、哈希表、滑动窗口

> 链接：[无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个字符串 s ，请你找出其中不含有重复字符的**最长子串**的长度。

{{< /tab >}}
{{< tab name="解法" >}}
**滑动窗口**

依旧从头开始，但这次引入一个哈希表来计算当前字符是否有重复。没有就一直扩大，顺便记录窗口大小；如果重复了，就一直缩短窗口直到没有重复字符，然后再记录窗口大小。时间复杂度$O(n)$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码:

```go
func lengthOfLongestSubstring(s string) (ans int) {
    cnt := [128]int{}
    left := 0
    for right, c := range s {
        cnt[c]++
        for cnt[c] > 1 {
            cnt[s[left]]--
            left++
        }
        ans = max(ans, right-left+1)
    }
    return
}
```

---

# 串联所有单词的子串
> 难度：困难

> 标签：数组、哈希表、滑动窗口

> 链接：[串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个字符串 s 和一个字符串数组`words`。`words`中所有字符串**长度相同**。

s 中的**串联子串**是指一个包含`words`中所有字符串以任意顺序排列连接起来的子串。

- 例如，如果`words = ["ab","cd","ef"]`， 那么`"abcdef"，"abefcd"，"cdabef"，"cdefab"，"efabcd"和 "efcdab"`都是串联子串。`"acdbef"`不是串联子串，因为他不是任何`words`排列的连接。

返回所有串联子串在 s 中的开始索引。你可以以**任意顺序**返回答案。
{{< /tab >}}
{{< tab name="解法" >}}
**滑动窗口**

困难题困难做(bushi)

1. 因为每个单词一样长，每次我们都是检测等长的一个子串，所以先定义一个`wordLen`表示一个单词的长度，可以推出定长窗口`windowLen = len(words) * wordLen`。
2. 引入哈希表`targetCnt`记录`words`中每个单词的出现次数，`cnt`记录主串中每个等长单词的出现次数，以及`overload`记录超出正确次数的单词数目。
3. 开始遍历，每次我们都把`wordLen`长度的子串加入窗口，判断`cnt[curWord]`是否等于`targetCnt[curWord]`，如果相等，说明新进来的这个数要不是原来有的超出了，要不是根本没有多余的。不管如何我们的`overload`都 +1。
4. 判断完，我们就看现在到没到窗口长度了，没到就继续把新单词加进来，到长度就看有没有超出的单词，没有的话那就匹配成功，把下标加入`ans`。然后不管有没有，我们都要缩短窗口，移出左边的那个单词。因为是定长窗口，所以移出一个就够了不用循环。然后移出去之后如果`cnt[curWord] == targetCnt[curWord]`，说明把超出的移出去了，那`overload`就要 -1。

时间复杂度$O(n)$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码:

```go
func findSubstring(s string, words []string) (ans []int) {
    wordLen := len(words[0])
    windowLen := wordLen * len(words)

    targetCnt := map[string]int{}
    for _, w := range words {
        targetCnt[w]++
    }

    for start := range wordLen {
        cnt := map[string]int{}
        overload := 0

        for right := start + wordLen; right <= len(s); right += wordLen {
            inWord := s[right-wordLen : right]

            if cnt[inWord] == targetCnt[inWord] {
                overload++
            }
            cnt[inWord]++

            left := right - windowLen
            if left < 0 {
                continue
            }

            if overload == 0 {
                ans = append(ans, left)
            }

            outWord := s[left : left+wordLen]
            cnt[outWord]--
            if cnt[outWord] == targetCnt[outWord] {
                overload--
            }
        }
    }
    return
}
```

---

# 最小覆盖子串
> 难度：困难

> 标签：数组、哈希表、滑动窗口

> 链接：[最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定两个字符串 s 和 t，长度分别是 m 和 n，返回 s 中的**最短窗口**子串，使得该子串包含 t 中的每一个字符（包括重复字符）。如果没有这样的子串，返回空字符串 ""。

测试用例保证答案唯一。

{{< /tab >}}
{{< tab name="解法" >}}
**滑动窗口**

依旧困难题。

// TODO

时间复杂度$O(n)$，空间复杂度$O(1)$。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码:

```go
// TODO
```