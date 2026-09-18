+++
date = '2026-09-18T10:10:48+08:00'
draft = false
title = 'Leetcode 面试150题——数组与字符串 总结'
summary = '归纳这个部分每道题的题解，使用Go语言'
tags = ['Leetcode', '题解汇总']
series = ['面试150题']
featured = true
math = true
+++

# 合并两个有序数组
> 难度：简单

> 标签：数组、双指针、排序

{{< tabgroup >}}
{{< tab name="题干" >}}
给你两个按`非递减顺序`排列的整数数组`nums1`和`nums2`，另有两个整数 m 和 n ，分别表示`nums1`和`nums2`中的元素数目。

请你 合并`nums2`到`nums1`中，使合并后的数组同样按`非递减顺序`排列。

注意：最终，合并后数组不应由函数返回，而是存储在数组`nums1`中。为了应对这种情况，`nums1`的初始长度为 m + n，其中前 m 个元素表示应合并的元素，后 n 个元素为 0 ，应忽略。`nums2`的长度为 n 。
{{< /tab >}}
{{< tab name="解法" >}}
这道题的解法有三种：
1. **合并后排序**。比较简单无脑。时间复杂度$O((m+n)\log(m+n))$，空间复杂度是$O(\log(m+n))$。
2. **正向双指针**。用一个额外的数组，然后每次从两个数组的头部取较小的数存进去。时间复杂度和空间复杂度都是$O(m+n)$。
3. **逆向双指针**。原地修改，每次从两个数组尾部取较大的数存到`num1`尾部的空白里。时间复杂度$O(m+n)$，空间复杂度$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

这里只给出解法3的代码，因为题目优化也是希望可以实现原地修改。
```go
func merge(nums1 []int, m int, nums2 []int, n int) {
	tail := m + n - 1
	p1, p2 := m-1, n-1
	for p2 >= 0 {
		if p1 >= 0 && nums1[p1] >= nums2[p2] {
			nums1[tail] = nums1[p1]
			p1--
		} else {
			nums1[tail] = nums2[p2]
			p2--
		}
        tail--
	}
}
```
---

# 移除元素
> 难度：简单

> 标签：数组、双指针

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个数组`nums`和一个值`val`，你需要**原地**移除所有数值等于`val`的元素。元素的顺序可能发生改变。然后返回`nums`中与`val`不同的元素的数量。

假设`nums`中不等于`val`的元素数量为 k，要通过此题，您需要执行以下操作：

- 更改`nums`数组，使`nums`的前 k 个元素包含不等于`val`的元素。`nums`的其余元素和`nums`的大小并不重要。
- 返回 k。
{{< /tab >}}
{{< tab name="解法" >}}
这道题的解法有两种：
1. **双指针1**。两个指针都从头出发，先移动右指针，每次遍历判断`nums[right]`是否等于`val`。如果等于，说明这个值要移除，我们就单纯移动右指针；如果不等于，说明这个值要保留，我们就让`nums[left] = nums[right]`，然后左右指针同时移动。时间复杂度$O(n)$，空间复杂度是$O(1)$。
2. **双指针2**。原先双指针的遍历移动有些繁琐。实际上我们观察题目会发现，这道题移除的元素都是放到数组末尾的。所以我们可以让两个指针分别位于数组的首尾，移动左指针，遇到`nums[left] == val`就让`nums[left] = nums[right]`。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出两种解法的实现代码。

{{< tabgroup >}}
{{< tab name="双指针一" >}}
```go
func removeElement(nums []int, val int) int {
    left := 0
    for _, v := range nums {
        if v != val {
            nums[left] = v
            left++
        }
    }
    return left
}
```
{{< /tab >}}
{{< tab name="双指针二" >}}
```go
func removeElement(nums []int, val int) int {
    left, right := 0, len(nums)
    for left < right {
        if nums[left] == val {
            nums[left] = nums[right-1]
            right--
        } else {
            left++
        }
    }
    return left
}
```
{{< /tab >}}
{{< /tabgroup >}}
    
---

# 删除有序数组中的重复项
> 难度：简单

> 标签：数组、双指针

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个`非严格递增排列`的数组`nums`，请你**原地**删除重复出现的元素，使每个元素**只出现一次**，返回删除后数组的新长度。元素的**相对顺序**应该保持**一致**。然后返回`nums`中唯一元素的个数。

考虑`nums`的唯一元素的数量为 k。去重后，返回唯一元素的数量 k。

`nums`的前 k 个元素应包含**排序后**的唯一数字。下标 k - 1 之后的剩余元素可以忽略。
{{< /tab >}}
{{< tab name="解法" >}}
**双指针**

定义一个变量`lastDiff`存储上一个不同的数。两个指针都从头出发，先移动右指针，每次遍历判断`nums[right]`是否等于`lastDiff`。如果等于，说明这个值有了，我们就单纯移动右指针；如果不等于，说明这个值要保留，我们就让`nums[left] = nums[right]`，然后左右指针同时移动，记录当前值为新的`lastDiff`。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出实现代码。

```go
func removeDuplicates(nums []int) int {
    left, lastDiff := 0, -200

    for _, num := range nums {
        if num != lastDiff {
            nums[left] = num
            lastDiff = num
            left++
        }
    }

    return left
}
```
---

# 删除有序数组中的重复项 II
> 难度：中等

> 标签：数组、双指针

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个有序数组`nums`，请你**原地**删除重复出现的元素，使得出现次数超过两次的元素只出现**两次** ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在**原地**修改输入数组 并在使用$O(1)$额外空间的条件下完成。
{{< /tab >}}
{{< tab name="解法" >}}
**双指针**

两个指针都从位置2出发，先移动右指针，每次遍历判断`nums[right]`是否等于`nums[left]`(就是上上个元素，保证每个数字有两个)。如果等于，说明这个值有两个了，我们就单纯移动右指针；如果不等于，说明这个值要保留，我们就让`nums[left] = nums[right]`，然后左右指针同时移动。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出实现代码。

```go
func removeDuplicates(nums []int) int {
    n := len(nums)
    if n <= 2 {
        return n
    }
    left, right := 2, 2
    for right < n {
        if nums[left-2] != nums[right] {
            nums[left] = nums[right]
            left++
        }
        right++
    }
    return left
}
```
---

# 多数元素
> 难度：简单

> 标签：数组、哈希表、分治、计数、排序、摩尔投票算法

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个大小为 n 的数组`nums`，返回其中的多数元素。多数元素是指在数组中出现次数`大于 ⌊ n/2 ⌋`的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。
{{< /tab >}}
{{< tab name="解法" >}}
这道题说白了就是求众数，解法比较多样，我挑了几个解法学习：
1. **哈希表**。用一个哈希表去记录每个数字出现的次数。如果当前这个数字超过了最大次数`cnt`，我们就把它记为当前的众数`majority`。一直更新直到结束。时间复杂度$O(n)$，空间复杂度是$O(n)$。
2. **排序**。既然是求众数，那我就先对数组进行排序。因为题干假定了众数必然存在，所以下标为`⌊ n/2 ⌋`的一定就是众数。时间复杂度$O(n\log n)$，空间复杂度是$O(\log n)$。
3. **随机化**。感觉这个方法有点哭笑不得。由于众数占据数据一半以上的下标，所以我们可以每次随机选择一个下标，然后计算这个下标对应的数在数组里的数量是否超过一半。因为是众数所以随机到需要的次数挺少的。时间复杂度$O(n)$，空间复杂度是$O(1)$。
4. **摩尔投票**。求绝对众数的一个很精彩的方法。因为众数在数组里有一半以上，所以众数的数量大于剩下所有元素的个数之和。所以我们可以定义一个量`vote`，每次遇到众数+1，遇到非众数-1(初始化第一个数是众数)。同时我们可以推得，如果前面x个数的票数和是0，那么剩下数的票数必定大于0，众数始终是不变的。所以当票数为0，我们就设置当前位置的数是众数继续执行。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面解法顺序给出题解。

{{< tabgroup >}}
{{< tab name="哈希表" >}}
```go
func majorityElement(nums []int) int {
    hash := map[int]int{}
    majority, cnt := 0, 0
    for _, num := range nums {
        hash[num]++
        if hash[num] > cnt {
            majority = num
            cnt = hash[num]
        }
    }
    return majority
}
```
{{< /tab >}}
{{< tab name="排序" >}}
```go
func majorityElement(nums []int) int {
    slices.Sort(nums)
    return nums[len(nums) / 2]
}
```
{{< /tab >}}
{{< tab name="随机化" >}}
```go
func majorityElement(nums []int) int {
    n := len(nums)
    for {
        rand.Seed(time.Now().UnixNano())
        candidate := nums[rand.Intn(n)]
        count := 0
        for _, num := range nums {
            if num == candidate {
                count++
            }
        }
        if count > n / 2 {
            return candidate
        }
    }
    return -1
}
```
{{< /tab >}}
{{< tab name="摩尔投票" >}}
```go
func majorityElement(nums []int) (ans int) {
    vote := 0
    for _, num := range nums {
        if vote == 0 {
            ans, vote = num, 1
        } else if num == ans {
            vote++
        } else {
            vote--
        }
    }
    return
}
```
{{< /tab >}}
{{< /tabgroup >}}

---

# 轮转数组
> 难度：中等

> 标签：数组、数学、双指针

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个整数数组`nums`，将数组中的元素向右轮转 k 个位置，其中 k 是非负数。

{{< /tab >}}
{{< tab name="解法" >}}
**反转数组**

先对整个数组进行反转，然后将前 k 个元素反转，再对剩下的元素进行反转即可。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出解法。

```go
func rotate(nums []int, k int)  {
    k %= len(nums)
    slices.Reverse(nums)
    slices.Reverse(nums[:k])
    slices.Reverse(nums[k:])
}
```

---

# 买卖股票的最佳时机
> 难度：简单

> 标签：数组、动态规划

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个数组`prices`，它的第 i 个元素`prices[i]`表示一支给定股票第 i 天的价格。

你只能选择**某一天**买入这只股票，并选择在**未来的某一个不同的日子**卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

{{< /tab >}}
{{< tab name="解法" >}}
**动态规划**

我们先定义三个变量：`price`当天价格，`minCost`最低成本，`maxProfit`最大利润。

每获取到一个`price`，我们就比较是不是`minCost`，是的话设置新的最低成本。然后计算当前利润，和`maxProfit`比较，大于的话设置新的最大利润，最后返回`maxProfit`。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出解法。

```go
func maxProfit(prices []int) int {
    minCost, maxProfit := prices[0], 0
    for _, price := range prices {
        minCost = min(minCost, price)
        maxProfit = max(maxProfit, price - minCost)
    }
    return maxProfit
}
```

---

# 买卖股票的最佳时机 II
> 难度：中等

> 标签：数组、动态规划、贪心

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个整数数组`prices`，其中`prices[i]`表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候**最多**只能持有**一股**股票。然而，你可以在**同一天**多次买卖该股票，但要确保你持有的股票不超过一股。

返回你能获得的**最大**利润。

{{< /tab >}}
{{< tab name="解法" >}}
**动态规划 + 贪心算法**

这道题的判断是，只要明天的价格比今天高，那我就今天买明天卖。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出解法。

```go
func maxProfit(prices []int) int {
    profit := 0
    n := len(prices)
    for i := range n-1 {
        x, y := prices[i], prices[i+1]
        if x < y {
            profit += y-x
        }
    }
    return profit
}
```
---

# 跳跃游戏
> 难度：中等

> 标签：数组、动态规划、贪心

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个非负整数数组`nums`，你最初位于数组的**第一个下标**。数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个下标，如果可以，返回`true`；否则，返回`false`。

{{< /tab >}}
{{< tab name="解法" >}}
**动态规划 + 贪心算法**

定义一个变量`distance`记录最远可达下标。遍历数组的每一个元素，记录当前位置可以到达的最远下标，与`distance`进行比较，大于的话则更新`distance`。如果当前下标大于`distance`，那就说明到不了，`return false`。时间复杂度$O(n)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出解法。

```go
func canJump(nums []int) bool {
    distance := 0
    for i, jump := range nums {
        if i > distance {
            return false
        }
        distance = max(distance, i+jump)
    }
    return true
}
```

---

# 跳跃游戏 II
> 难度：中等

> 标签：数组、动态规划、贪心

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个长度为 n 的 0 索引整数数组`nums`。初始位置在下标 0。

每个元素`nums[i]`表示从索引 i 向后跳转的最大长度。换句话说，如果你在索引 i 处，你可以跳转到任意 (i + j) 处：

- `0 <= j <= nums[i]`且
- `i + j < n`

返回到达 n - 1 的最小跳跃次数。测试用例保证可以到达 n - 1。

{{< /tab >}}
{{< tab name="解法" >}}
这道题解法也是**动态规划 + 贪心算法**，但是有两种贪心的策略：
1. **正向贪心**。定义两个变量`curEnd`和`nextEnd`，分别代表当前的最远边界和下一个最远边界。从贪心的角度，每次跳我们都跳到最远，那最后需要的次数就是最少的。从第一个元素出发，不断更新下一个最远边界，当走到当前边界，我就跳到下一个最远边界，步数+1。时间复杂度$O(n)$，空间复杂度是$O(1)$。
2. **反向贪心**。因为我们总要跳到最后一个位置，不妨就从最后出发，往前找能够跳到最后的下标。如果存在多个下标，为了少跳，就要选择最远的那个下标，不断更新和查找直到回到下标0。时间复杂度$O(n^2)$，空间复杂度是$O(1)$。
{{< /tab >}}
{{< /tabgroup >}}

下面给出解法。

{{< tabgroup >}}
{{< tab name="正向贪心" >}}
```go
func jump(nums []int) (ans int) {
    curEnd := 0
    nextEnd := 0
    for i, num := range nums[:len(nums)-1] {
        nextEnd = max(nextEnd, i+num)
        if i == curEnd {
            curEnd = nextEnd
            ans++
        }
    }
    return
}
```

{{< /tab >}}
{{< tab name="反向贪心" >}}
```go
func jump(nums []int) (ans int) {
    pos := len(nums) - 1
    for pos > 0 {
        for i := 0; i < pos; i++ {
            if i + nums[i] >= pos {
                pos = i
                ans++
                break
            }
        }
    }
    return
}
```
{{< /tab >}}
{{< /tabgroup >}}

---