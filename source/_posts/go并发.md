---
title: go并发
date: 2019-04-17 18:48:03
tags: [go, 并发]
categories: [go]
---

## 管道 channel

### 关闭管道

```go

func main(){
	ch := make(chan int, 3)
	go func(){
		for i:=0;i<15;i++{
			ch <- i
		}
		close(ch)
	}()
	// for {
	// 	i, ok := <-ch
	// 	if ok{
	// 		fmt.Println(i)
	// 	}else{
	// 		break
	// 	}
	// }
	// for i, ok := <-ch; ok;i, ok = <-ch {
	// 	fmt.Println(i)
	// }
	for i := range ch{
		fmt.Println(i)
	}
}
```

通过close可以关闭管道，关闭后，不能写入东西，不能读取。可以通过ok判断。也可以通过range遍历

### 单向管道

```go
func main(){
	ch := make(chan int)
	var readch <-chan int = ch
	var writech chan<- int = ch
	// writech<- 0
	// fmt.Println(<-readch)
	// readch <- 0 编译不通过
	// <- writech 编译不通过
	go func(){
		for i:=0;i<20;i++{
			writech <- i
		}
		close(ch)
	}()
	for i := range readch{
		fmt.Println(i)
	}
}
```

### 有无缓冲管道

```go
func main(){
	ch := make(chan int, 5)
	// ch := make(chan int)
	go func(){
		for i:=0;i<10;i++{
			ch <- i
			fmt.Println("生成：",i)
		}
		close(ch)
	}()
	for i := range ch{
		fmt.Println("消费：",i)
		time.Sleep(time.Second)
	}
}
```
可以看到有缓冲管道在装满时，阻塞。

## runtime的使用

### 让出时间片

```go
func main(){
	go func() {
		for i := 0;i<5;i++{
			fmt.Println("go")
		}
	}()
	for i := 0;i<2;i++{
		fmt.Println("hello")
	}
}

```

主线程运行结束，go协程运行没有结束，就会被自动关闭。会没有go的输出。让出时间片，就可以看到go协程的输出。


```go

func main(){
	go func() {
		for i := 0;i<3;i++{
			fmt.Println("go")
		}
	}()
	for i := 0;i<2;i++{
		runtime.Gosched() // 让出时间片
		fmt.Println("hello")
	}
}

```

### 退出子线程

```go
func test(){
	defer fmt.Println("cc")
	// return  
	// runtime.Goexit()
	fmt.Println("dd")
}

func main(){
	ch := make(chan int)
	go func(ch chan int){
		fmt.Println("aa")
		test()
		fmt.Println("bb")
		defer func(ch chan int){
			ch <- 0
		}(ch)
	}(ch)
	<-ch
}
```

输出：
		aa
		dd
		cc
		bb

取消return的注释后， 会输出：
		aa
		cc
		bb

由此说明，return退出的是函数

取消 runtime.Goexit() 的注释，会输出：
		aa
		cc

说明退出了整个线程

### 最大并行数

```go

func main(){
	n := runtime.GOMAXPROCS(4)
	fmt.Println(n)
	for {
		go fmt.Print("0")
		fmt.Print(1)
	}
}
```

## time的使用

### 定时器的创建

```go

func main(){
	timer := time.NewTimer(2 * time.Second)
	fmt.Println("时间",time.Now())

	t := <-timer.C
	fmt.Println(t)
}

```

定时2秒钟。

### sleep

```go

func main(){
	fmt.Println(time.Now())
	time.Sleep(2* time.Second)
	fmt.Println(time.Now())
}


```

### After

```go
func main(){
	fmt.Println(time.Now())
	<-time.After(2*time.Second)
	fmt.Println(time.Now())
}
```

### stop

```go
func main(){
	timer := time.NewTimer(time.Second)
	go func(){
		<-timer.C
		fmt.Println("携程关闭")
	}()
	// timer.Stop()
	for{

	}
}
```

取消timer.Stop()的注释，子线程将不会结束。因为在stop以后，timer.C 管道中不会有信息。

### Reset

```go
func main(){
	fmt.Println(time.Now())
	timer := time.NewTimer(3 * time.Second)
	timer.Reset(time.Second)
	<-timer.C
	fmt.Println(time.Now())
}
```
重新设置定时器时间

### Ticker

```go
func main(){
	ticker := time.NewTicker(time.Second)
	i:= 0
	for _ = range ticker.C {
		fmt.Println("i=",i)
		i ++
		if i ==5{
			ticker.Stop()
			break
		}
	}
}
```

可以多次使用的定时器
