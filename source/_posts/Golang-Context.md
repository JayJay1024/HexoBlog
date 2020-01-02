---
title: 为什么要用Context(上下文)
date: 2020-01-01 18:30:54
tags: [Golang]
categories: 攻城狮那些事儿
---

## 写在前面
相比在参数中传递用于控制流程的自定义管道变量，`Context`可以更方便地串联、管理多个`Goroutine`，在大型项目中发挥着重要的作用。

<!--more-->

## New Context

#### WithCancel
```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
```
返回一个携带一个新的`Done`管道的`parent`副本，`parent`的`cancel`被执行或`parent`的`Done`被`close`的时候，副本的`Done`会被`close`。

```go
func main() {
	stoping := make(chan bool)
	ctx, cancel := context.WithCancel(context.Background())

	go check(ctx, stoping)
	time.Sleep(3 * time.Second)

	fmt.Println("after 3s")
	cancel()

	<-stoping
}

func check(ctx context.Context, stoping chan bool) {
	fmt.Println("[check] start...")
	<-ctx.Done()
	fmt.Println("[check] end...")
	stoping <- true
}
```
输出：
```bash
[check] start...
after 3s
[check] end...
```

#### WithDeadline
```go
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
```
副本`deadline`或`cancel`的时候，或`parent`的`Done`被`close`的时候，副本的`Done`将`close`

#### WithTimeout
```go
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```
返回`WithDeadline(parent, time.Now().Add(timeout))`。

```go
func main() {
	ctx, _ := context.WithTimeout(context.Background(), 2*time.Second)

	go check(ctx, 1*time.Second)

	select {
	case <-ctx.Done():
		fmt.Println("[main] ", ctx.Err())
	}
}

func check(ctx context.Context, timeout time.Duration) {
	select {
	case <-ctx.Done():
		fmt.Println("[check] ", ctx.Err())
	case <-time.After(timeout):
		fmt.Println("[check] timeout")
	}
}
```
输出：
```bash
[check] timeout
[main]  context deadline exceeded
```

#### WithValue
```go
func WithValue(parent Context, key interface{}, val interface{}) Context
```

```go
func main() {
	stoping := make(chan bool)
	ctx := context.WithValue(context.Background(), "name", "Jack")

	go check(ctx, stoping)
	<-stoping
}

func check(ctx context.Context, stoping chan bool) {
	fmt.Println("[check] Hi", ctx.Value("name"))
	stoping <- true
}
```
输出：
```bash
[check] Hi Jack
```

#### Background
`Background returns a non-nil, empty Context. It is never canceled, has no values, and has no deadline`。这个一般在`main`函数、初始化、测试和顶层使用，然后通过它往后派生。

#### TODO
几乎是`Background`的别名般的存在，但相比`Background`，一些静态分析工具可以通过`TODO`分析`Context`有没有正确地传递。一般在对`Context`的使用还不清晰的地方使用。

## Note
- `Context`被`cancel`的时候，关联的资源也会被释放
- 不要在`struct`中存储`Context`实例，明确地通过函数参数传递，并且一般作为第一个参数
- 即使函数运行，也不要传递`nil Context`，如果不确定传递的`Context`将来有什么用处，可以传递一个`context.TODO`
- 通过`Context`携带参数一般只用于请求域数据，可选参数应该明确通过函数参数传递
- 同一个`Context`可运行于不同`Goroutines`中的函数，`Context`可以在多个`Goroutines`间同步