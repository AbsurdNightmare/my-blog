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