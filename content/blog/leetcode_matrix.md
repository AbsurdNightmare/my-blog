+++
date = '2026-09-28T09:59:48+08:00'
draft = false
title = 'Leetcode 面试150题——矩阵 总结'
summary = '矩阵部分题解汇总'
tags = ['Leetcode', '题解汇总', '矩阵']
series = ['面试150题']
featured = false
math = true
+++

# 有效的数独
> 难度：中等 

> 标签：数组、哈希表、矩阵

> 链接：[有效的数独](https://leetcode.cn/problems/valid-sudoku/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
请你判断一个 9 x 9 的数独是否有效。只需要**根据以下规则**，验证已经填入的数字是否有效即可。

- 数字 1-9 在每一行只能出现一次。
- 数字 1-9 在每一列只能出现一次。
- 数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。
 

注意：

- 一个有效的数独（部分已被填充）不一定是可解的。
- 只需要根据以上规则，验证已经填入的数字是否有效即可。
- 空白格用 '.' 表示。

{{< /tab >}}
{{< tab name="解法" >}}
**哈希表**

有行、列、块三种需要记录数字个数的，那我们就开三个对应的哈希表进行记录就好了。因为只要知道存在与否，所以可以用布尔数组更方便。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func isValidSudoku(board [][]byte) bool {
    var rowHash, colHash, boxHash [9][9]bool

    for i, row := range board {
        for j, v := range row {
            if v == '.' {
                continue
            }
            c := v - '1'
            loc := (i/3)*3 + j/3
            if rowHash[i][c] || colHash[j][c] || boxHash[loc][c] {
                return false
            }
            rowHash[i][c] = true
            colHash[j][c] = true
            boxHash[loc][c] = true
        }
    }
    return true
}
```

---

# 螺旋矩阵
> 难度：中等 

> 标签：数组、模拟、矩阵

> 链接：[螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个 m 行 n 列的矩阵`matrix`，请按照**顺时针螺旋顺序**，返回矩阵中的所有元素。

{{< /tab >}}
{{< tab name="解法" >}}
经典题。顺时针螺旋分解开就是四个方向：向右、向下、向左、向上。我们按照顺序输出，然后不断缩短边界。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func spiralOrder(matrix [][]int) (ans []int) {
    left, right, up, down := 0, len(matrix[0])-1, 0, len(matrix)-1
    for {
        for i := left; i <= right; i++ {
            ans = append(ans, matrix[up][i])
        }
        up++
        if up > down {
            break
        }
        for i := up; i <= down; i++ {
            ans = append(ans, matrix[i][right])
        }
        right--
        if right < left {
            break
        }
        for i := right; i >= left; i-- {
            ans = append(ans, matrix[down][i])
        }
        down--
        if down < up {
            break
        }
        for i := down; i >= up; i-- {
            ans = append(ans, matrix[i][left])
        }
        left++
        if left > right {
            break
        }
    }
    return
}
```

---

# 旋转图像  
> 难度：中等 

> 标签：数组、数学、矩阵

> 链接：[旋转图像](https://leetcode.cn/problems/rotate-image/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个 n × n 的二维矩阵`matrix`表示一个图像。请你将图像顺时针旋转**90**度。

你必须在**原地**旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要**使用另一个矩阵来旋转图像**。

{{< /tab >}}
{{< tab name="解法" >}}
这个题取巧的手段就是把**旋转90度**变成两步：
1. 转置矩阵，即交换`matrix[i][j]`和`matrix[j][i]`的值。
2. 对称反转列。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func rotate(matrix [][]int) {
    n := len(matrix)
    for i := range n {
        for j := range i { 
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        }
    }

    for _, row := range matrix {
        slices.Reverse(row)
    }
}
```

---