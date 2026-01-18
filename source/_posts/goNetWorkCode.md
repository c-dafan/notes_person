---
title: go网络编程
date: 2019-04-22 21:00:47
tags: [go, 网络编程, 计算机网络]
categories: [go]
---

## 连接过程

### 监听端口

```go

listener, err := net.Listen("tcp", ":8000")
defer listener.Close()

```

### 客户端发送请求链接

```go

conn, err := net.Dial("tcp", ":8000")
defer conn.Close()

```

### 接受客户端请求链接

```go
conn, err := listener.Accept()
defer conn.Close()
```

### 接收数据

```go
buffer := make([]byte, 2048)
n, err := conn.Read(buffer)
```

### 发送数据

```go
n, err = conn.Write([]byte(。。。))
```

## server处理单client

### 服务器

```go
func main() {
	listener, err := net.Listen("tcp", ":8000")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer listener.Close()
	buffer := make([]byte, 2048)
	conn, err := listener.Accept()
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(conn.RemoteAddr())
	n, err := conn.Read(buffer)
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(string(buffer[:n]))
	defer conn.Close()
}

```

### client

```go

func main() {
	conn, err := net.Dial("tcp", ":8000")
	defer conn.Close()
	if err != nil {
		fmt.Println(err)
		return
	}
	buf := make([]byte, 2048)
	content := "hello world"
	n, err := conn.Write([]byte(content))
	if err != nil {
		fmt.Println(err)
	}
	fmt.Println(n)
}

```

## server并发处理多client，并保持链接状态

### server

这是使用了go开启一个线程， 当有新的client链接时，都会开启一个go程。
如果把go去掉，只能处理单个client，当多个client请求链接时，只有第一个和server保持链接。其余的都在等待状态。有个教学视频说会被替换，经过测试，当不使用go时，线程会被阻塞到读取的位置，只是其余的client在等待，第一个链接的不会被替换。

```go
func main() {
	listener, err := net.Listen("tcp", ":8000")
	if err != nil {
		fmt.Println(err)
		return
	}
	defer listener.Close()
	for {
		conn, err := listener.Accept()
		if err!=nil{
			fmt.Println(err)
		}
		go handle(conn)
	}
}

func handle(conn net.Conn) {
	fmt.Println(conn.RemoteAddr())
	defer conn.Close()
	buffer := make([]byte, 2048)
	for{
		n, err := conn.Read(buffer)
		if err != nil {
			fmt.Println(err)
			return
		}
		content := strings.Trim(string(buffer[:n]), " \r\n")
		fmt.Println(n, content)
		if content == "exit"{
			return
		}
		fmt.Println(time.Now())
		n, err = conn.Write([]byte(time.Now().String()))
		if err != nil {
			fmt.Println(err)
		}
	}
}
```

### client

输入exit退出

```go

func main() {
	conn, err := net.Dial("tcp", ":8000")
	defer conn.Close()
	if err != nil {
		fmt.Println(err)
		return
	}
	go func() {
		for {
			str := make([]byte, 2048)
			n, err := os.Stdin.Read(str)
			if err != nil{
				fmt.Println(err)
				return
			}
			n, err = conn.Write(str[:n])
			if err != nil {
				fmt.Println(err)
				return
			}
		}
	}()
	buf := make([]byte, 2048)
	for{
		n, err := conn.Read(buf)
		if err != nil{
			fmt.Println(err)
			return
		}
		fmt.Println(string(buf[:n]))
	}
}

```
