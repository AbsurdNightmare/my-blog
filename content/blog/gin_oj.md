+++
date = '2026-09-17T19:19:42+08:00'
draft = false
title = '从零构建一个在线判题系统 (OJ)'
summary = '基于Gin框架，涉及架构设计，Docker沙箱和踩过的坑'
tags = ['项目']
series = ['项目']
featured = false
+++

今天分享一个我做的在线判题平台 [Gin_OJ](https://github.com/AbsurdNightmare/Gin-OJ)

相信各位特别是计算机相关专业的同学，都用过Online Judge，也就是在线判题平台。无论是学校的 OJ，还是比较知名的洛谷、Leetcode、CodeForce，要写一个真正判题的 OJ，重点是要解决三件事：
1. 怎么安全地跑用户提交的代码？
2. 怎么准确判定超时和内存超限？
3. 怎么不泄露测试数据？

解决这三件事，是我们搭建一个 OJ 最重要的环节。下面就让我基于我自己搭建的 Gin_OJ 项目，来讲讲怎么从零构建一个在线判题系统。

> 注意：本博客还是需要读者有一定的Go语言以及Gin框架的基础，没有学习过的同学可以先去B站学习一下。

## 0. 这个项目是什么

让我们先来围绕“在线判题系统”这几个字，好好地思考一下，我们想要实现的功能是什么样的。

站在用户在角度，我们应该要实现这么一些功能：

- 浏览题目列表，按难度/关键词搜索
- 打开题目，看到题目描述、样例输入输出，可能还有一些标签、提示之类的
- 在网页上选语言、写代码、点提交
- 系统在后台编译并运行你的代码，用若干组测试数据验证，返回 `Accepted` / `Wrong Answer` / `Time Limit Exceeded` 等结果
- 看到每个测试点的耗时、内存、通过情况
- 讨论区、排名榜、管理后台

而我们站在开发者的角度，需要思考的就不只是用户希望的功能了。其中最关键的一点，就是用户提交上来的代码。用户可能写了一个死循环、申请了100GB的内存、尝试访问内部数据等等。这些肯定都是不被允许，或者需要我们去考虑的。

> 所以，我们设计的重心，是如何让用户提交的代码在一个安全的“黑盒子”里运行，并且我们可以准确计算消耗的时间和内存。

这边介绍一下这次用到的技术栈：

| 分层 | 技术 |
|---|---|
| 后端 | Go 1.26 · Gin · GORM · MySQL · Redis |
| 判题沙箱 | Docker（一次性容器 + cgroup + ulimit） |
| 前端 | Vue 3 · TypeScript · Vite · Pinia · Vue Router |
| 鉴权 | JWT |
| 文档 | Swagger |

其中的关键就是通过 Docker 去封装整一个判题沙箱，使得这个部分对用户的提交代码做出正确的应对，同时也把这个环节模块化了。

> 这里简单讲一下什么是“沙箱 (Sandbox)”：沙箱是一种安全隔离的运行环境。它只能访问内部有的数据和信息，无法触及外部的内容。通过沙箱我们可以很好地保护系统数据信息不会被用户的代码访问甚至修改。

---

## 1. 整体架构

### 1.1 分层

```
                    ┌─────────────┐
   HTTP 请求  ────▶ │  routers/   │  路由注册：URL → 处理函数
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │ middlewares │  JWT 鉴权、管理员校验
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │    api/     │  控制器：解析参数、组装响应
                    └──────┬──────┘
                           ▼
                    ┌─────────────┐
                    │  services/  │  业务逻辑：校验、事务、组装数据
                    └──────┬──────┘
                     ┌─────┴─────┐
                     ▼           ▼
              ┌───────────┐  ┌────────┐
              │ models/   │  │ judge/ │  判题引擎（独立子系统）
              │ (GORM)    │  └───┬────┘
              └───────────┘      ▼
                           ┌──────────────┐
                           │ judge/sandbox│  Docker 沙箱
                           └──────────────┘
```

从这个设计中我们可以学到关于软件设计分层的重要性：`每层只做一件事`

- `api/` 只负责HTTP的相关工作：把 JSON 解析成结构体、把结果转成响应。这一层**不写业务规则**。
- `services/` 放业务规则：比如"提交的代码不能超过 64KB"、"题目至少要有一个测试用例"、"更新题目要在事务里"。它不关心请求是从 HTTP 来的还是从 CLI 来的。
- `models/` 这一层包含三块：`database/` 是数据库表映射，`request/` 是请求参数，`response/` 是响应结构。**请求和响应分开定义**，这样数据库字段变更不会直接泄漏到 API 上。
- `judge/` 是一个**独立子系统**，不依赖 HTTP 层。它甚至不依赖数据库的 CRUD 逻辑，只是读一次题目和测试用例。

这样分层最好的就是把判题引擎单独写集成测试，不需要起 HTTP 服务。

### 1.2 一次请求的完整生命周期

这里我们以“提交代码”这样的一次请求为例子来讨论。

```
用户点击提交
   │
   ▼
POST /api/auth/submission          ← routers/submission.go 注册的路由
   │
   ▼
AuthCheck 中间件                    ← 校验 JWT，把 user_id 放进 context
   │
   ▼
SubmissionApi.CreateSubmission     ← api/submission.go
   │  1. ShouldBindJSON 解析参数
   │  2. 从 context 取 user_id
   ▼
SubmissionService.CreateSubmission ← services/submission.go
   │  1. 校验语言是否支持
   │  2. 校验代码长度
   │  3. 校验题目是否存在
   │  4. 事务：写入 submission 记录 + 题目提交数+1 + 用户提交数+1
   │  5. 调用 judge.EngineApp.Submit() 入队
   ▼
HTTP 立刻返回 { submission_id }
   │
   ┄┄┄ 以下是异步的 ┄┄┄
   ▼
judge 引擎的 worker 从队列取出
   │
   ▼
在 Docker 沙箱里编译 → 逐测试点运行 → 比对输出
   │
   ▼
把判决结果写回 submissions 表
```

这里我做的一个关键设计，就是提交是一个异步操作。HTTP 请求只负责把用户的提交记下来并放进队列，然后立刻返回。判题在后台进行，前端轮询结果。否则用户提交一个要跑 10 秒的代码，HTTP 请求就得挂 10 秒，很容易超时。

---

## 2. 判题引擎

### 2.1 思考：为什么不能啥也不管直接跑代码

我们可以想到一个简单的实现：

```go
func Judge(code string) {
    // 1. 写文件
    os.WriteFile("main.cpp", []byte(code), 0644)
    // 2. 编译
    exec.Command("g++", "-o", "main", "main.cpp").Run()
    // 3. 跑测试用例
    for _, tc := range testCases {
        cmd := exec.Command("./main")
        cmd.Stdin = strings.NewReader(tc.Input)
        out, _ := cmd.Output()
        if out != tc.Output { return "WA" }
    }
}
```

虽然这一段代码是可以跑起来，结果也没什么问题，但是却存在四个致命问题：
1. **没做资源隔离**。用户如果在这个`main.cpp`文件里写`while (1) {}`，服务器的 CPU 就直接占满了，陷入死循环出不来了。如果用户写的是 `system("rm -rf /")`，那完了，服务器没了。
2. **没做超时限制**。没出结果进程就一直跑一直跑，会给服务器带来极大的负担。
3. **无法准确测量资源**。你想知道这个程序跑多长时间，用多少内存，Go 的这个 exec 包都做不了。
4. **无限制并发**。每个提交开一个 goroutine 的话，用户提交一千次就有一千个 goroutine ，纯纯自毁行为。

### 2.2 解决方案：队列 + 固定大小 worker 池


我们可以定义一个判题引擎结构：

```go
type Engine struct {
    cfg     config.Judge
    sbx     sandbox.Sandbox   // 沙箱后端
    queue   chan task         // 有缓冲的队列
    wg      sync.WaitGroup

    mu      sync.Mutex
    pending map[string]int    // userID → 正在排队+判题的提交数
    closed  bool
}
```

启动时开固定数量的 worker：

```go
func (e *Engine) start() {
    for i := 0; i < e.cfg.Concurrency; i++ {
        e.wg.Add(1)
        go e.worker(i)
    }
    e.requeueOrphans()   // 见 2.4
}

func (e *Engine) worker(n int) {
    defer e.wg.Done()
    for t := range e.queue {          // 从队列消费，队列空了就阻塞
        func() {
            defer e.release(t.userID) // 无论成功失败都要归还配额
            e.judgeOne(t.submissionID)
        }()
    }
}
```

这样做的好处：

- **负载可控**：无论来多少提交，同时判题的数量永远是`Concurrency`个。提交量很大的时候也只会让队列变长，不会把服务器打垮。
- **有明确的背压**：队列满了就拒绝新提交并返回明确原因，而不是默默堆积到内存爆掉。

入队时还有两层保护：

```go
func (e *Engine) Submit(submissionID, userID string) error {
    e.mu.Lock()
    if e.closed { e.mu.Unlock(); return ErrClosed }

    // 保护一：单用户配额。防止一个人刷满整个队列，让其他人排不上。
    if pending := e.pending[userID]; pending >= e.cfg.MaxPendingPerUser {
        e.mu.Unlock()
        return fmt.Errorf("你已有 %d 个提交在排队或判题中，请等待完成后再提交", pending)
    }
    e.pending[userID]++
    e.mu.Unlock()

    // 保护二：队列容量。满了直接拒绝，不阻塞。
    select {
    case e.queue <- task{submissionID: submissionID, userID: userID}:
        return nil
    default:
        e.release(userID)
        return ErrQueueFull
    }
}
```

> 注意`e.pending[userID]++`放在**发送之前**。如果放在之后，可能出现"worker 已经判完并调用了 release，但计数还没加上去"的竞态，导致计数变成负数。