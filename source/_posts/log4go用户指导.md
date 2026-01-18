---
title: log4go用户指导
date: 2019-04-26 14:52:41
tags: [go, 日志]
categories: [go]
---

## quick start

### 获取

```bash
go get github.com/c-dafan/log4go
```

### 导入

```go
import l4g "github.com/c-dafan/log4go"
```


### 获取logger


#### 全局 logger

```go
l4g.Info("hello world")
defer l4g.Close()
```

#### 新建默认logger NewDefaultLogger

```go
log := l4g.NewDefaultLogger(l4g.INFO)
log.Info("hello world")
defer log.Close()
```

#### 新建logger l4g.Logger

```go
log := make(l4g.Logger)
defer log.Close()
log.AddFilter("stdout", l4g.DEBUG, l4g.NewConsoleLogWriter())
log.Info("hello world")
```

### 添加过滤器

```go
log := make(l4g.Logger)
defer log.Close()
log.AddFilter("stdout", l4g.DEBUG, l4g.NewConsoleLogWriter())
log.Info("hello world")
```

通过AddFilter函数添加过滤器，
AddFilter(name, level, writer)
第一个参数： 过滤器名字，随意， 只要不重复就行
第二个参数： 日志级别
第三个参数： 输出器

### 输出日志

```go
l4g.Finest()
l4g.Fine()
l4g.Debug()
l4g.Trace()
l4g.Info()
l4g.Warning()
l4g.Error()
l4g.Critical()

```
对应于不同级别

## user guide


### 日志级别

```go
	FINEST
	FINE
	DEBUG
	TRACE
	INFO
	WARNING
	ERROR
	CRITICAL
```

### writer

#### 输出到控制台的writer

```go
log := make(l4g.Logger)
defer log.Close()
log.AddFilter("stdout", l4g.DEBUG, l4g.NewConsoleLogWriter())
log.Info("hello world")
```

#### 输出到文件的writer

```go
log := make(l4g.Logger)
flw := l4g.NewFileLogWriter(filename, false)

//		 %T - Time (15:04:05 MST)
//       %t - Time (15:04)
//       %D - Date (2006/01/02)
//       %d - Date (01/02/06)
//       %L - Level (FNST, FINE, DEBG, TRAC, WARN, EROR, CRIT)
//       %S - Source
//       %M - Message
//       It ignores unknown format strings (and removes them)
//       Recommended: "[%D %T] [%L] (%S) %M"

flw.SetFormat("[%D %T] [%L] (%S) %M")
flw.SetRotate(false)
flw.SetRotateSize(0)
flw.SetRotateLines(0)
flw.SetRotateDaily(false)
flw.SetRotateMaxBackup(999)
log.AddFilter("file", l4g.FINE, flw)
```

> NewFileLogWriter(filename string, rotate bool)

日志文件名字，是否多文件

> SetFormat(format string)

设置输出格式

> flw.SetHeadFoot(head string, foot string)

日志头和脚

> SetRotate(rotate bool)

设置是否多文件，需要和NewFileLogWriter一致

> SetRotateSize(maxsize int)

设置一个文件大小， 0不限制

> SetRotateLines(maxlines int)

设置一个文件多少行， 0不限制

> SetRotateDaily(daily bool)

每天一个日志文件

> SetRotateMaxBackup(maxbackup int)

默认999， 最多多少个日志文件

### 配置文件

#### 配置文件编写

```xml
<logging>
  <filter enabled="true">
    <tag>stdout</tag>
    <type>console</type>
    <!-- level is (:?FINEST|FINE|DEBUG|TRACE|INFO|WARNING|ERROR) -->
    <level>DEBUG</level>
  </filter>
  <filter enabled="true">
    <tag>file</tag>
    <type>file</type>
    <level>FINEST</level>
    <property name="filename">test{}.log</property>
    <!--
       %T - Time (15:04:05 MST)
       %t - Time (15:04)
       %D - Date (2006/01/02)
       %d - Date (01/02/06)
       %L - Level (FNST, FINE, DEBG, TRAC, WARN, EROR, CRIT)
       %S - Source
       %M - Message
       It ignores unknown format strings (and removes them)
       Recommended: "[%D %T] [%L] (%S) %M"
    -->
    <property name="format">[%D %T] [%L] (%S) %M</property>
    <property name="rotate">false</property> <!-- true enables log rotation, otherwise append -->
    <property name="maxsize">0M</property> <!-- \d+[KMG]? Suffixes are in terms of 2**10 -->
    <property name="maxlines">0K</property> <!-- \d+[KMG]? Suffixes are in terms of thousands -->
    <property name="maxbackup">999</property> 
	<property name="daily">true</property> <!-- Automatically rotates when a log message is written after midnight -->
  </filter>
</logging>
```

[example.json](https://github.com/c-dafan/log4go/blob/master/examples/example.json)

#### 配置文件导入

[XMLConfigurationExample.go](https://github.com/c-dafan/log4go/blob/master/examples/XMLConfigurationExample.go)

[JsonConfigExample.go](https://github.com/c-dafan/log4go/blob/master/examples/JsonConfigExample.go)

## issue

日志输出用的是go线程，一旦主线程结束，go线程也会跟着结束，所以go线程中没有运行完的，会直接停止。这个样子



