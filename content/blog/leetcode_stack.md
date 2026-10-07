+++
date = '2026-10-07T14:08:13+08:00'
draft = false
title = 'Leetcode 面试150题——栈 总结'
summary = '栈部分题解汇总'
tags = ['Leetcode', '题解汇总', '栈']
series = ['面试150题']
featured = false
math = true
+++

> 说在前：由于是栈板块，所以采用的方法基本都是栈或者栈的思想。其他的方法基本跳过或者不考虑。

---

# 有效的括号
> 难度：简单

> 标签：栈、字符串、括号序列

> 链接：[有效的括号](https://leetcode.cn/problems/valid-parentheses/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给定一个只包括`'('，')'，'{'，'}'，'['，']'`的字符串 s ，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。
3. 每个右括号都有一个对应的相同类型的左括号。

{{< /tab >}}
{{< tab name="解法" >}}
可以有两种思路：
1. 每次遇到左边的括号就入栈，遇到右边的就弹出栈顶看看对不对应，对应就继续；
2. 每次遇到左边括号就入栈对应的右边括号，遇到右边就比较是不是一样的，一样就继续。
两种都可以，下面解法给出的是第二种。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func isValid(s string) bool {
    if len(s)%2 != 0 {
        return false
    }

    st := []rune{}
    for _, c := range s {
        switch c {
            case '(':
                st = append(st, ')')
            case '[':
                st = append(st, ']')
            case '{':
                st = append(st, '}')
            default:
                if len(st) == 0 || st[len(st)-1] != c {
                    return false
                }
                st = st[:len(st)-1]
        }
    }
    return len(st) == 0
}
```

---

# 简化路径
> 难度：中等

> 标签：栈、字符串

> 链接：[简化路径](https://leetcode.cn/problems/simplify-path/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个字符串`path`，表示指向某一文件或目录的`Unix`风格**绝对路径**（以 '/' 开头），请你将其转化为**更加简洁的规范路径**。

在`Unix`风格的文件系统中规则如下：

- 一个点 '.' 表示当前目录本身。
- 此外，两个点 '..' 表示将目录切换到上一级（指向父目录）。
- 任意多个连续的斜杠（即，'//' 或 '///'）都被视为单个斜杠 '/'。
- 任何其他格式的点（例如，'...' 或 '....'）均被视为有效的文件/目录名称。

返回的**简化路径**必须遵循下述格式：

- 始终以斜杠 '/' 开头。
- 两个目录名之间必须只有一个斜杠 '/' 。
- 最后一个目录名（如果存在）不能 以 '/' 结尾。
- 此外，路径仅包含从根目录到目标文件或目录的路径上的目录（即，不含 '.' 或 '..'）。

返回简化后得到的**规范路径**。

{{< /tab >}}
{{< tab name="解法" >}}
先根据斜杠分割字符串，这一步可以快速解决多个斜杠一起的问题。然后遍历字符串栈，遇到“.”和空白直接跳过，遇到“..”就把栈顶出栈，其他的字符串就直接入栈。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func simplifyPath(path string) string {
    strs := strings.Split(path, "/")
    ans := []string{}
    for _, str := range strs {
        if str == "." || str == "" {
            continue
        }
        if str != ".." {
            ans = append(ans, str)
        } else if len(ans) > 0 {
            ans = ans[:len(ans)-1]
        }
    }
    return "/" + strings.Join(ans, "/")
}
```

---

# 最小栈
> 难度：中等

> 标签：栈、设计

> 链接：[最小栈](https://leetcode.cn/problems/min-stack/description/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
设计一个支持`push`，`pop`，`top`操作，并能在常数时间内检索到最小元素的栈。

实现`MinStack`类:

- `MinStack()`初始化堆栈对象。
- `void push(int value)`将元素`value`推入堆栈。
- `void pop()`删除堆栈顶部的元素。
- `int top()`获取堆栈顶部的元素。
- `int getMin()`获取堆栈中的最小元素。

{{< /tab >}}
{{< tab name="解法" >}}
这道题其实最主要就是时间复杂度要求在常数时间，所以这个最小元素我们就必须用一个变量来进行存储。因为是栈是有顺序的，所以我们每次进来和栈顶比较，如果比栈顶的小那就把新栈顶设置为这个更小的数。这样栈顶永远保存的是栈里最小的值。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
type pair struct { val, preMin int }

type MinStack []pair

func Constructor() MinStack {
    return MinStack{{0, math.MaxInt}}
}


func (this *MinStack) Push(value int)  {
    *this = append(*this, pair{value, min(this.GetMin(), value)})
}


func (this *MinStack) Pop()  {
    *this = (*this)[:len(*this)-1]
}


func (this MinStack) Top() int {
    return this[len(this)-1].val
}


func (this MinStack) GetMin() int {
    return this[len(this)-1].preMin
}
```

---

# 逆波兰表达式求值
> 难度：中等

> 标签：栈、数组、数学

> 链接：[逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个字符串数组`tokens`，表示一个根据**逆波兰表示法**表示的算术表达式。

请你计算该表达式。返回一个表示表达式值的整数。

注意：

- 有效的算符为`'+'、'-'、'*' 和 '/'`。
- 每个操作数（运算对象）都可以是一个整数或者另一个表达式。
- 两个整数之间的除法总是**向零截断**。
- 表达式中不含除零运算。
- 输入是一个根据逆波兰表示法表示的算术表达式。
- 答案及所有中间计算结果可以用**32 位**整数表示。

{{< /tab >}}
{{< tab name="解法" >}}
这个是栈里面很经典的题了。既然运算符都在后面，那就每次遇到运算符就弹出栈顶两个元素进行运算，然后把结果重新入栈即可。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func evalRPN(tokens []string) int {
    stk := []int{}
    for _, num := range tokens {
        x, err := strconv.Atoi(num)
        if err == nil {
            stk = append(stk, x)
            continue
        }

        x = stk[len(stk)-1]
        stk = stk[:len(stk)-1]
        switch num[0] {
            case '+':
                stk[len(stk)-1] += x
            case '-':
                stk[len(stk)-1] -= x
            case '*':
                stk[len(stk)-1] *= x
            default:
                stk[len(stk)-1] /= x
        }
    }
    return stk[0]
}
```

---

# 基本计算器
> 难度：困难

> 标签：栈、递归、数学、字符串

> 链接：[基本计算器](https://leetcode.cn/problems/basic-calculator/?envType=study-plan-v2&envId=top-interview-150)

{{< tabgroup >}}
{{< tab name="题干" >}}
给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。

注意:不允许使用任何将字符串作为数学表达式计算的内置函数，比如`eval()`。

{{< /tab >}}
{{< tab name="解法" >}}
这个题目的思路其实就是把括号一层层翻译成正负号来算：用一个`ops`栈存每一层括号外的符号基准，初始放个 1 表示整体是正数，`sign`表示当前数字最终该带的符号。遇到`'+'`就把`sign`恢复成当前栈顶符号，遇到`'-'`就取它的相反数；遇到`'('`说明进入新的一层，把当前`sign`压栈当作括号内基准，遇到`')'`就弹栈回到外层。数字部分直接逐位读成`num`，然后`ans += sign * num`。

{{< /tab >}}
{{< /tabgroup >}}

下面给出详细代码：

```go
func calculate(s string) (ans int) {
    ops := []int{1}
    sign := 1
    n := len(s)
    for i := 0; i < n; {
        switch s[i] {
        case ' ':
            i++
        case '+':
            sign = ops[len(ops)-1]
            i++
        case '-':
            sign = -ops[len(ops)-1]
            i++
        case '(':
            ops = append(ops, sign)
            i++
        case ')':
            ops = ops[:len(ops)-1]
            i++
        default:
            num := 0
            for ; i < n && '0' <= s[i] && s[i] <= '9'; i++ {
                num = num*10 + int(s[i]-'0')
            }
            ans += sign * num
        }
    }
    return
}
```