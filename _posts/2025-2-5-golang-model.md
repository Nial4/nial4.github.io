---
layout: post
title: '关于Golang的设计模式'
tags: [golang, promgramming]
comments: true
---

Golang 中的并行设计模式：errgroup 与 MapReduce 实践指南

## 概述

在 Go 语言开发中，高效处理并发任务是提升系统性能的关键。咱主要对比两种典型的并行模式（`errgroup`和`MapReduce`），分析它们的差异与适用场景。

---

### 1. errgroup 模式

#### 设计理念

- **轻量级并行控制**：基于标准库实现，适合简单并行任务
- **快速失败机制**：任一子任务失败立即取消所有任务
- **Context 集成**：天然支持超时和手动取消

#### 典型实现

```go
func FetchAllData() (*Result, error) {
    g, ctx := errgroup.WithContext(context.Background())
    var resA DataA
    var resB DataB

    g.Go(func() error {
        data, err := serviceA.Call(ctx)
        resA = data
        return err
    })

    g.Go(func() error {
        data, err := serviceB.Call(ctx)
        resB = data
        return err
    })

    if err := g.Wait(); err != nil {
        return nil, err
    }

    return &Result{A: resA, B: resB}, nil
}
```

#### 特性

- 代码直白简单
- 需预定义结果变量
- 适合固定数量任务

---

### 2.MapReduce 模式

#### 设计理念

- 结构化数据流：明确分为生成(Generate)→ 映射(Map)→ 归约(Reduce)三阶段
- 工作池机制：内置 Worker 控制并发数
- 弹性扩展：天然支持动态任务列表

#### 典型实现

```go
type Result struct {
    A DataA
    B DataB
}

func ProcessDynamicTasks() (*Result, error) {
    return MapReduce(
        // Generate阶段：动态生成任务
        func(ch chan<- string) {
            for _, task := range config.GetTasks() {
                ch <- task
            }
        },
        // Map阶段：并行处理任务
        func(task string, writer Writer[any], cancel func(error)) {
            data, err := process(task)
            if err != nil {
                cancel(err)
                return
            }
            writer.Write(data)
        },
        // Reduce阶段：聚合结果
        func(ch <-chan any, writer Writer[*Result], cancel func(error)) {
            res := &Result{}
            for data := range ch {
                switch v := data.(type) {
                case DataA: res.A = v
                case DataB: res.B = v
                }
            }
            writer.Write(res)
        },
        WithWorkers(10),
    )
}
```

#### 特性

- 自动处理结果收集
- 支持动态任务扩展
- 内置 panic 恢复机制

这里参考了 Golang 的微服务框架：GoZero 的 MR 模式实现，更多细节可参考[GoZero](https://github.com/zeromicro/go-zero/blob/master/core/mr/readme.md)。

---

### 场景对比

#### 适合使用 errgroup 的场景（业务逻辑层面）

固定数量的微服务调用

```go
// 并发调用用户服务、订单服务、支付服务
services := []func() error{getUser, getOrder, getPayment}
```

批量资源初始化

```go
// 并行初始化数据库、缓存、消息队列
inits := []func() error{initDB, initCache, initMQ}
```

快速失败型任务

```go
// 任一健康检查失败立即返回
checks := []func() error{checkAPI, checkDB, checkDisk}
```

#### 适合使用 MapReduce 的场景(微服务层面)

数据处理流水线

```go
// 日志处理：收集→解析→统计
GenerateLogs → ParseLog → CountMetrics
```

动态任务执行

```go
// 根据用户输入动态生成分析任务
userSelectedTasks := getDynamicTasks()
```

大规模数据批处理

```go
// 处理 10 万条数据：分片 → 处理 → 聚合
WithWorkers(100) // 使用 100 个 worker
```

---

### 我自己的一个实际工作中的设计

我称之为模块化单体微服务，即将一个大的单体应用拆分成多个模块，每个模块都是一个独立的功能，通过 Channel 进行模块之间的通信，每个模块都是一个独立的 goroutine。

之前在开发中遇到了一个 task，需要 5 分钟进行一次，从数据库中拉去信息提交给 FFmpeg 进行处理，本地做 fingerprints，然后提交到云端，最后再保存。本身这个 task 并不难，因为单个 batch 可能数据量很多，要满足 5 分钟一次的话，FFmpeg 的部分可能会出现上一 batch 还没处理完，下一 batch 就来了的情况，所以我将这个 task 拆分成了几个模块。

```go
todo
```

....todo
