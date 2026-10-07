+++
date = '2026-09-28T13:28:54+08:00'
draft = false
title = 'Leetcode 面试150题——哈希表 总结'
summary = '哈希表部分题解汇总'
tags = ['Leetcode', '题解汇总', '哈希表']
series = ['面试150题']
featured = false
math = true
+++

> 说在前：由于是哈希表板块，所以采用的方法基本都是哈希表。其他的方法基本跳过或者不考虑。

---

# 赎金信
> 难度：简单

> 标签：字符串、哈希表、计数

> 链接：[赎金信](https://leetcode.cn/problems/ransom-note/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你两个字符串：`ransomNote`和`magazine`，判断`ransomNote`能不能由`magazine`里面的字符构成。

如果可以，返回`true`；否则返回`false`。

`magazine`中的每个字符只能在`ransomNote`中使用一次。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

简单题简单做，一个哈希表。先把`magazine`里面有的字符计数一下，然后到`ransomNote`里面消耗，如果刚好或者还有剩那就是可以的。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func canConstruct(ransomNote string, magazine string) bool {
    if len(magazine) < len(ransomNote) {
        return false
    }

    hash := [128]int{}
    for _, c := range magazine {
        hash[c]++
    }

    for _, c := range ransomNote {
        hash[c]--
        if hash[c] < 0 {
            return false
        }
    }
    return true
}
```

---

# 同构字符串
> 难度：简单

> 标签：字符串、哈希表、计数

> 链接：[同构字符串](https://leetcode.cn/problems/isomorphic-strings/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定两个字符串 s 和 t ，判断它们是否是同构的。

如果 s 中的字符可以按某种映射关系替换得到 t ，那么这两个字符串是同构的。

每个出现的字符都应当映射到另一个字符，同时不改变字符的顺序。不同字符不能映射到同一个字符上，相同字符只能映射到同一个字符上，字符可以映射到自己本身。

{{< /tab >}}
{{< tab name="解法" >}}
1. **哈希表**。两个方向的映射，也就是双射关系，我们可以用两个哈希表来存储两个方向，每次都检查是否存在两个方向的映射，出错了就返回`false`。
2. **索引**。取巧的理解就是，每个字符第一次出现对应的下标，在两个字符串的位置必须一样，不然就会出错。


{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

{{< tabgroup >}}
{{< tab name = "哈希表">}}
```go
func isIsomorphic(s string, t string) bool {
    s2t := [128]byte{}
    t2s := [129]byte{}

    for i := range s {
        x, y := s[i], t[i]
        if s2t[x] > 0 && s2t[x] != y || t2s[y] > 0 && t2s[y] != x {
            return false
        }
        s2t[x] = y
        t2s[y] = x
    }
    return true
}
```

{{< /tab >}}
{{< tab name="索引" >}}
```go
func isIsomorphic(s, t string) bool {
    for i := 0; i < len(s); i++ {
        if strings.IndexByte(s, s[i]) != strings.IndexByte(t, t[i]) {
            return false
        }
    }
    return true
}
```

{{< /tab >}}
{{< /tabgroup >}}

---

# 单词规律
> 难度：简单

> 标签：字符串、哈希表、计数

> 链接：[单词规律](https://leetcode.cn/problems/word-pattern/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一种规律`pattern`和一个字符串 s ，判断 s 是否遵循相同的规律。

这里的**遵循**指完全匹配，例如，`pattern`里的每个字母和字符串 s 中的每个非空单词之间存在着双向连接的对应规律。具体来说：

- `pattern`中的每个字母都**恰好**映射到 s 中的一个唯一单词。
- s 中的每个唯一单词都**恰好**映射到`pattern`中的一个字母。
- 没有两个字母映射到同一个单词，也没有两个单词映射到同一个字母。

{{< /tab >}}
{{< tab name="解法" >}}
1. **哈希表**。两个方向的映射，也就是双射关系，我们可以用两个哈希表来存储两个方向，每次都检查是否存在两个方向的映射，出错了就返回`false`。
2. **索引**。取巧的理解就是，每个字符第一次出现对应的下标，在两个字符串的位置必须一样，不然就会出错。


{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

{{< tabgroup >}}
{{< tab name = "哈希表">}}
```go
func isIsomorphic(s string, t string) bool {
    s2t := [128]byte{}
    t2s := [129]byte{}

    for i := range s {
        x, y := s[i], t[i]
        if s2t[x] > 0 && s2t[x] != y || t2s[y] > 0 && t2s[y] != x {
            return false
        }
        s2t[x] = y
        t2s[y] = x
    }
    return true
}
```

{{< /tab >}}
{{< tab name="索引" >}}
```go
func isIsomorphic(s, t string) bool {
    for i := 0; i < len(s); i++ {
        if strings.IndexByte(s, s[i]) != strings.IndexByte(t, t[i]) {
            return false
        }
    }
    return true
}
```

{{< /tab >}}
{{< /tabgroup >}}

---

# 有效的字母异位词
> 难度：简单

> 标签：字符串、哈希表、排序

> 链接：[有效的字母异位词](https://leetcode.cn/problems/valid-anagram/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定两个字符串 s 和 t ，编写一个函数来判断 t 是否是 s 的 字母异位词。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

只是位置不一样但是每个字母的数量是一样的，那我们就可以用哈希表记录 s 中的所有字母数量，然后检查 t 中字母是否符合。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func isAnagram(s string, t string) bool {
    hash := [26]int{}
    for _, c := range s {
        hash[c-'a']++
    }
    for _, c := range t {
        hash[c-'a']--
    }
    return hash == [26]int{}
}
```

---

# 字母异位词分组
> 难度：简单

> 标签：字符串、哈希表、排序、数组

> 链接：[字母异位词分组](https://leetcode.cn/problems/group-anagrams/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个字符串数组，请你将**字母异位词**组合在一起。可以按任意顺序返回结果列表。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

字母异位词除了上一道题的数量相同，还有一个特性就是按照字母排序之后是一样的。对于这道题这么多词我们就得采用这种判断方式。用哈希表记录，key 是每个词排序后的结果，value 是原来状态。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func groupAnagrams(strs []string) [][]string {
    hash := map[string][]string{}
    for _, s := range strs {
        tmp := []byte(s)
        slices.Sort(tmp)
        str := string(tmp)
        hash[str] = append(hash[str], s)
    }
    return slices.Collect(maps.Values(hash))
}
```

---

# 两数之和
> 难度：简单

> 标签：哈希表、数组

> 链接：[两数之和](https://leetcode.cn/problems/two-sum/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个整数数组`nums`和一个整数目标值`target`，请你在该数组中找出**和为目标值`target`**的那**两个**整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案，并且你不能使用两次相同的元素。

你可以按任意顺序返回答案。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

两数之和为`target`，那么当我固定一个值 a，我就要找数组里是否存在一个值为`target - a`的数。所以我们可以遍历数组，每次都在哈希表里查找，如果找到了就返回，没找到就把当前值加入哈希表中。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func twoSum(nums []int, target int) []int {
    hash := map[int]int{}
    for i, v := range nums {
        if j, ok := hash[target-v]; i != j && ok {
            return []int{i, j}
        }
        hash[v] = i
    }
    return nil
}
```

---

# 快乐数
> 难度：中等

> 标签：哈希表、数学、双指针、Floyd判圈算法

> 链接：[快乐数](https://leetcode.cn/problems/happy-number/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
编写一个算法来判断一个数 n 是不是快乐数。

**「快乐数」**定义为：

- 对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和。
- 然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。
- 如果这个过程 结果为 1，那么这个数就是快乐数。

如果 n 是 快乐数 就返回`true`；不是，则返回`false`。

{{< /tab >}}
{{< tab name="解法" >}}
这道题本事就是判断循环。判圈算法用的多的还是第二种解法的快慢指针，不过既然这道题放在哈希表里面，那就多一个哈希表解法。
1. **哈希表**。这个好理解，利用哈希表存储数据判断是否重复，有重复的说明循环了。
2. **快慢指针**。顾名思义，一个指针一次走多一点，一个指针一次走少一点。具体来说就是慢指针一次执行一次任务，快指针一次执行两次任务。这样的话，如果出现循环，那么快指针总会在某个时候追上慢指针。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

{{< tabgroup >}}
{{< tab name="哈希表" >}}

```go
func isHappy(n int) bool {
    m := map[int]bool{}
    for ; n != 1 && !m[n]; n, m[n] = step(n), true { }
    return n == 1
}

func step(n int) int {
    sum := 0
    for n > 0 {
        sum += (n%10) * (n%10)
        n = n/10
    }
    return sum
}
```

{{< /tab >}}
{{< tab name="快慢指针" >}}

```go
func isHappy(n int) bool {
    slow, fast := n, n
    for {
        slow = bitSquareSum(slow)
        fast = bitSquareSum(fast)
        fast = bitSquareSum(fast)
        if slow == fast {
            break
        }
    }
    return slow == 1
}

func bitSquareSum(n int) int {
    sum := 0
    for n > 0 {
        bit := n % 10
        sum += bit * bit
        n = n / 10
    }
    return sum
}
```
{{< /tab >}}
{{< /tabgroup >}}

---

# 存在重复元素 II
> 难度：简单

> 标签：哈希表、数组

> 链接：[两数之和](https://leetcode.cn/problems/two-sum/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个整数数组`nums`和一个整数 k ，判断数组中是否存在两个**不同的索引** i 和 j ，满足`nums[i] == nums[j]`且`abs(i - j) <= k`。如果存在，返回`true`；否则，返回`false`。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

和两数之和思路差不多，就是多一个差值的判断。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func containsNearbyDuplicate(nums []int, k int) bool {
    hash := map[int]int{}
    for i, v := range nums {
        if j, ok := hash[v]; ok {
            if i - j <= k {
                return true
            }
        }
        hash[v] = i
    }
    return false
}
```

---

# 最长连续序列
> 难度：中等

> 标签：哈希表、数组、并查集

> 链接：[最长连续序列](https://leetcode.cn/problems/longest-consecutive-sequence/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个未排序的整数数组`nums`，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为$O(n)$的算法解决此问题。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

这道题要求用$O(n)$的算法，那就不能排序了。我们可以先把所有的数存到一个布尔哈希里，然后遍历哈希表。如果当前值的前一个数字不存在，说明这个值是一个序列的开头，我们就一个一个查找哈希表，看往后的序列可以进行到哪，然后再更新最大值。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func longestConsecutive(nums []int) int {
	hash := map[int]bool{}
	for _, v := range nums {
		hash[v] = true
	}
	ans := 0
	for x := range hash {
		if hash[x-1] {
			continue
		}

		y := x + 1
		for hash[y] {
			y++
		}
		ans = max(ans, y-x)
	}
	return ans
}
```