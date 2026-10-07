+++
date = '2026-10-07T14:08:05+08:00'
draft = false
title = 'Leetcode 面试150题——区间 总结'
summary = '区间部分题解汇总'
tags = ['Leetcode', '题解汇总', '区间']
series = ['面试150题']
featured = false
math = true
+++

# 汇总区间
> 难度：简单

> 标签：数组

> 链接：[汇总区间](https://leetcode.cn/problems/summary-ranges/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个**无重复元素**的**有序**整数数组`nums`。

区间`[a,b]`是从 a 到 b（包含）的所有整数的集合。

返回**恰好覆盖数组中所有数字**的**最小有序**区间范围列表 。也就是说，`nums`的每个元素都恰好被某个区间范围所覆盖，并且不存在属于某个区间但不属于`nums`的数字 x 。

列表中的每个区间范围`[a,b]`应该按如下格式输出： 

- `"a->b"`，如果`a != b`
- `"a"`，如果`a == b`

{{< /tab >}}
{{< tab name="解法" >}}
因为数组是有序的，所以我们只要遍历数组，用两个指针指向每次的头尾数字，然后遇到前后差不是 1 的就加到输出的数组里。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func summaryRanges(nums []int) []string {
	i := 0
	ans := []string{}
	for j, v := range nums {
		if j == len(nums)-1 || v+1 != nums[j+1] {
			if i == j {
				ans = append(ans, strconv.Itoa(nums[i]))
			} else {
				ans = append(ans, strconv.Itoa(nums[i])+"->"+strconv.Itoa(nums[j]))
			}
			i = j + 1
		}
	}
    return ans
}
```

---

# 合并区间
> 难度：中等

> 标签：数组、排序、快速排序

> 链接：[合并区间](https://leetcode.cn/problems/merge-intervals/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
以数组`intervals`表示若干个区间的集合，其中单个区间为`intervals[i] = [starti, endi]`。请你合并所有重叠的区间，并返回*一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间*。

{{< /tab >}}
{{< tab name="解法" >}}
我们先把数组按照区间起始点升序排序。然后遍历数组，如果一个区间的开头小于上一个区间的终点，说明二者可以合并，合并时注意终点不是第二个区间的终点，而是两个区间终点的最大值。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func merge(intervals [][]int) [][]int {
    slices.SortFunc(intervals, func(i, j []int) int {
        return i[0] - j[0]
    })

    ans := [][]int{}
    start, end := intervals[0][0], intervals[0][1]

    for _, row := range intervals {
        if row[0] <= end {
            end = max(end, row[1])
        } else {
            ans = append(ans, []int{start, end})
            start, end = row[0], row[1]
        }
    }
    ans = append(ans, []int{start, end})
    return ans
}
```

---

# 插入区间
> 难度：中等

> 标签：数组

> 链接：[插入区间](https://leetcode.cn/problems/insert-interval/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个**无重叠的**，按照区间起始端点排序的区间列表`intervals`，其中`intervals[i] = [start_i, end_i]`表示第 i 个区间的开始和结束，并且`intervals`按照`start_i`升序排列。同样给定一个区间`newInterval = [start, end]`表示另一个区间的开始和结束。

如果两个区间**至少**共享一个点，则认为它们是重叠的。

在`intervals`中插入区间`newInterval`，使得`intervals`依然按照`start_i`升序排列，且区间之间不重叠（如果有必要的话，可以合并区间）。

返回插入之后的`intervals`。

**注意**你不需要原地修改`intervals`。你可以创建一个新数组然后返回它。

{{< /tab >}}
{{< tab name="解法" >}}
重点就是题干上的**不重叠**。因为给出的区间不是重叠的，所以我们可以不管那些和新区间没有重叠的前后部分，遍历的时候直接加到输出数组中。

![pic1](./interval.png "图1")

如果是和新区间有重叠的部分，我们就一边遍历一边合并，和上一道题思路差不多。但是我们可以修改新区间的范围，取左右的最远来涵盖重叠部分。然后把更新好的新区间加入输出数组即可。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func insert(intervals [][]int, newInterval []int) [][]int {
    ans := [][]int{}
    i, n := 0, len(intervals)
    for i < n && intervals[i][1] < newInterval[0] {
        ans = append(ans, intervals[i])
        i++
    }

    for i < n && intervals[i][0] <= newInterval[1] {
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i++
    }
    ans = append(ans, newInterval)

    for i < n {
        ans = append(ans, intervals[i])
        i++
    }
    return ans
}
```

---

# 用最少数量的箭引爆气球
> 难度：中等

> 标签：数组、贪心、排序

> 链接：[用最少数量的箭引爆气球](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
有一些球形气球贴在一堵用 XY 平面表示的墙面上。墙面上的气球记录在整数数组`points`，其中`points[i] = [x_start, x_end]`表示水平直径在`x_start`和`x_end`之间的气球。你不知道气球的确切 y 坐标。

一支弓箭可以沿着 x 轴从不同点**完全垂直**地射出。在坐标 x 处射出一支箭，若有一个气球的直径的开始和结束坐标为`x_start`，`x_end`， 且满足`x_start ≤ x ≤ x_end`，则该气球会被**引爆**。可以射出的弓箭的数量**没有限制**。 弓箭一旦被射出之后，可以无限地前进。

给你一个数组`points`，返回引爆所有气球所必须射出的**最小**弓箭数 。

{{< /tab >}}
{{< tab name="解法" >}}
**贪心**

这道题一样先排个序。要用最少的箭，可以先思考一下：
- 如果我们射箭点尽可能往左，那覆盖的数组会比较少，这样最后弓箭数会比较大；
- 如果我们射箭点尽可能往右，那可以一次可以覆盖很多个区间，那这样弓箭数就可以达到最小。

想好了之后，我们可以把每次的射箭点定在一个区间的终点，然后判断遍历区间看当前区间是否包含这个射箭点，如果包含说明这个地方的气球被引爆了，不需要额外的箭。一直到有一个区间不包含了，我们就更新一个新的射箭点。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func findMinArrowShots(points [][]int) (ans int) {
    slices.SortFunc(points, func(a, b []int) int { return a[1] - b[1] }) 
    pre := math.MinInt
    for _, p := range points {
        if p[0] > pre {
            ans++
            pre = p[1]
        }
    }
    return
}
```