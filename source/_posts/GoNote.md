---
title: GoNote
date: 2019-04-09 13:26:50
tags: [go, note]
categories: go
---


## 函数类型 匿名函数

go 函数也是一种类型，用于声明变量，接收变量值

```go
package main
import "fmt"
type myFunc func(a int, b int) int
func main(){
    var myfunc myFunc
    myfunc = func(a int, b int) int {
        return a+b
    }
    fmt.Println(myfunc(1,2))
}
```

## 回调函数

把函数当作一个参数

```go
type myFunc func(a int, b int) int

func calu(a int, b int, myfunc myFunc) int {
    return myfunc(a, b)
}

func main(){
    var myfunc myFunc
    myfunc = func(a int, b int) int {
        return a+b
    }
    fmt.Println(calu(1,2, myfunc))
}
```

## 闭包

闭包中变量，一直存在

```go
func test02() func() int{
    vv := 1
    return func() int{
        vv++
    return vv 
    }
}

func main(){
    f := test02()
    fmt.Println(f())
    fmt.Println(f())
}
```

## 匿名导入

```go
import _ "fmt"
```

执行init函数

## 匿名字段

继承

### 定义与初始化

```go

type people struct{
    name string
    age int
    sex byte
}

type student struct{
    people
    id int
}

func main()  {
    s := student{people{"aa",16,'m'},150706}
    fmt.Println(s)
    s2 := student{people:people{name:"bb"}    id:160706}
    fmt.Println(s2)
    // s3 := student{"aa",16,'m',150706} 错误用法
    s4 := student{}
    s4.id = 170706
    s4.name = "cc"
    s4.age = 17
    s4.sex = 'm'
    fmt.Println(s4)
}
```

### 非结构字段

```go

type people struct{
    name string
    age int
    sex byte
}

type student struct{
    people
    int
}

func main()  {
    s := student{people{"aa",16,'m'},150706}
    fmt.Println(s)
    s4 := student{}
    s4.int = 170706
    s4.name = "cc"
    s4.age = 17
    s4.sex = 'm'
    fmt.Println(s4)
}
```

### 同名字段

就近原则

## 方法

### 值与引用

```go
type people struct{
    name string
    age int
    sex byte
}

func (this *people) setinfoPointer(){
    this.name = "a"
    this.age = 12
    this.sex = 'm'
}

func (this people) setinfo(){
    this.name = "a"
    this.age = 12
    this.sex = 'm'
}

func main()  {
    p := people{}
    p.setinfo() // 无效
    fmt.Println(p)
    p.setinfoPointer()
    fmt.Println(p)
}
```

## 接口

### 空接口 类型断言

```go
type people struct{
    name string
    age int
    sex byte
}

func main()  {
    ii := make([]interface{}, 3)
    ii[0] = 1
    ii[1] = "2"
    ii[2] = people{name:"3"}
    for _,d := range ii {
        if val, ok:=d.(int); ok{
            fmt.Println("int",val)
        }else if val, ok:=d.(string); ok{
            fmt.Println("string",val)
        }else if val, ok:=d.(people); ok{
            fmt.Println("people",val)
        }
    }
    for _,d := range ii {
        switch value := d.(type) {
        case int:
            fmt.Println("int",value)
        case string:
            fmt.Println("string",value)
        case people:
            fmt.Println("people",value)
        }
    }
}
```