---
title: go 爬虫
date: 2019-04-09 17:36:56
tags: [go, spider]
categories: go
---

## 需要的

* net/http 请求网络
* 正则表达式 提取内容
* encode/json 解析json
* os 创建文件
* 并发

## 豆瓣电影评分

### 获取请求

```go
    response, err := http.Get(url)
    if err != nil {
        return
    }
    buf := make([]byte, 4096)
    var str string
    for true {
        n, err := response.Body.Read(buf)
        if n == 0 {
            break
        }
        if err != nil && err != io.EOF {
            return
        }
        str += string(buf[:n])
    }
```

### 正则表达式

通过正则表达式获取内容

```go
    reg1 := regexp.MustCompile(`<img width="100" alt="(?s:(.*?))"`)
    reg2 := regexp.MustCompile(`<span class="rating_num" property="v:average">(?s:(.*?))</span>`)
    reg3 := regexp.MustCompile(`<span>(?s:(\d*?))人评价</span>`)
    str1 := reg1.FindAllStringSubmatch(str, -1)
    str2 := reg2.FindAllStringSubmatch(str, -1)
    str3 := reg3.FindAllStringSubmatch(str, -1)
    for v := range str3{
        fmt.Println(str1[v],str2[v],str3[v])
    }
```

### 全部代码

```go

//https://movie.douban.com/top250?start=75&filter=
// <img width="100" alt="
//<span class="rating_num" property="v:average">8.7</span>
// <span>620194人评价</span>

func spider(url string, page chan int, v int) {
	response, err := http.Get(url)
	if err != nil {
		return
	}
	buf := make([]byte, 4096)
	var str string
	for true {
		n, err := response.Body.Read(buf)
		if n == 0 {
			break
		}
		if err != nil && err != io.EOF {
			return
		}
		str += string(buf[:n])
	}
	reg1 := regexp.MustCompile(`<img width="100" alt="(?s:(.*?))"`)
	reg2 := regexp.MustCompile(`<span class="rating_num" property="v:average">(?s:(.*?))</span>`)
	reg3 := regexp.MustCompile(`<span>(?s:(\d*?))人评价</span>`)
	str1 := reg1.FindAllStringSubmatch(str, -1)
	str2 := reg2.FindAllStringSubmatch(str, -1)
	str3 := reg3.FindAllStringSubmatch(str, -1)
	for v := range str3{
		fmt.Println(str1[v],str2[v],str3[v])
	}
	defer response.Body.Close()
	page <- v
}

func working(start int, end int) {
	page := make(chan int)
	for v := start; v <= end; v++ {
		url := fmt.Sprintf("https://movie.douban.com/top250?start=%d&filter=", v*25)
		go spider(url, page, v)
	}
	for v := start; v <= end; v++ {
		fmt.Println(<-page)
	}
}

func main() {
	var start, end int
	fmt.Scan(&start)
	fmt.Scan(&end)
	working(start, end)
}

```

## 斗鱼

斗鱼通过后端传json到前端，然后解析json，在前端显示。请求部分同上

### json解析部分

```go
    jsonContent:=make(map[string]interface{})
    _ = json.Unmarshal([]byte(str),&jsonContent)
    data := jsonContent["data"]
    data2 := data.(map[string]interface{})
    data3 := data2["rl"].([]interface{})
    for v := range data3{
        fmt.Println(data3[v].(map[string]interface{})["rs16"])
    }
```

json解析需要成一个string:interface{}的字典。空接口类型需要自己断言。一层一层找到需要的数据。

### 全部代码

```go

import (
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"strconv"
)

// https://www.douyu.com/gapi/rknc/directory/yzRec/4

func spider(v int, page chan int)  {
	url := "https://www.douyu.com/gapi/rknc/directory/yzRec/"+strconv.Itoa(v)
	response, err := http.Get(url)
	if err != nil{
		page <- v
		return
	}
	var str string
	buf := make([]byte, 4096)
	for true {
		n, err:= response.Body.Read(buf)
		if n == 0 {
			break
		}
		if err != nil && err != io.EOF {
			page <- v
			return
		}
		str += string(buf[:n])
	}
	defer response.Body.Close()

	//json.Unmarshal([]byte(str), )
	//reg := regexp.MustCompile(`(https://rpic.douyucdn.cn/live-cover/.*?)"`)
	//pngs := reg.FindAllStringSubmatch(str, -1)
	//for v:= range pngs{
	//	fmt.Println(pngs[v])
	//}
	jsonContent:=make(map[string]interface{})
	_ = json.Unmarshal([]byte(str),&jsonContent)
	data := jsonContent["data"]
	data2 := data.(map[string]interface{})
	data3 := data2["rl"].([]interface{})
	for v := range data3{
		fmt.Println(data3[v].(map[string]interface{})["rs16"])
	}
	page <- v
}

func DouSpider(start int, end int)  {
	page := make(chan int)
	for v := start; v <= end; v++ {
		go spider(v, page)
	}
	for v := start; v <= end; v++ {
		fmt.Println(<-page)
	}
}

func main() {
	var start, end int
	fmt.Scan(&start)
	fmt.Scan(&end)
	DouSpider(start, end)
}

```
