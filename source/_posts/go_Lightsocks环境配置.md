---
title: Lightsocks环境配置
date: 2019-04-12 09:03:48
tags: [go, 代理]
categories: [计算机网络]
---

[你也能写个 Shadowsocks](https://github.com/gwuhaolin/blog/issues/12)

## 环境搭建

通过SOCKS5代理，[代码见](https://github.com/gwuhaolin/lightsocks),

代码运行方法很简单，先运行server然后运行local，但是现在有个问题，如何才能让访问通过local的端口发出呢？
这个可以通过浏览器设置代理ip和端口，把代理ip和端口设置成local监听的端口，然后就可以访问到local。设置方法：

* 这里使用的是chrome，
* 进入设置
* 进入高级设置
* 代理设置
* 这时候会弹出系统弹出窗
* 局域网（LAN）设置
* 在新的弹出窗可以看到

现在可以访问local了，local也可以把数据转发到server，但是现在还有一个问题。server是直接把链接断掉，因为这个使用SOCKS5，server发现不是SOCKS5后，就丢弃了。local只是直接做了转发，需要浏览器直接设置为SOCKS5代理。

* 这个使用的是chrome，需要一个插件
* 下载插件[SwitchyOmega](https://github.com/FelisCatus/SwitchyOmega/releases)
* 进入扩展程序，打开开发者模式，把下载的crx文件直接拖进去就可以直接安装了
* 进入SwitchyOmega界面，
* 新建模式，填写协议，ip：port
* 应用选项。

## 扩展功能

在代码中，可见server.go 文件。在文件中，handleConn方法主要做转发处理。所以这里对handleConn方法，这修改就可以了。

### 输出日志

我这里需要建立链接的日志，日志内容包括时间和源ip与目标ip。做一下修改就可以。

> 首先修改securetcp中struct

```go
type Addr interface {
	Network() string // name of the network (for example, "tcp", "udp")
	String() string  // string form of address (for example, "192.0.2.1:25", "[2001:db8::1]:80")
}

// 加密传输的 TCP Socket
type SecureTCPConn struct {
	io.ReadWriteCloser
	Address Addr
	Cipher *cipher
}
````

> 然后在这里输出local->server日志即可

```go
    // 进行转发
    // 从 localUser 读取数据发送到 dstServer
    go func() {
        err := localConn.DecodeCopy(dstServer)
        log.Println(time.Now(),"目标地址：", dstServer.RemoteAddr(),"源地址：", localConn.Address) // 增加
        if err != nil {
            // 在 copy 的过程中可能会存在网络超时等 error 被 return，只要有一个发生了错误就退出本次工作
            localConn.Close()
            dstServer.Close()
        }
    }()
````

> server -> local 日志 需要做以下修改

```go
(&SecureTCPConn{
		Cipher:          localConn.Cipher,
		Address: dstServer.RemoteAddr(),
		ReadWriteCloser: dstServer,
	}).EncodeCopyServer(localConn) // 把EncodeCopy改为EncodeCopyServer方法
```

> 在securetcp创建EncodeCopyServer方法

```go
func (secureSocket *SecureTCPConn) EncodeCopyServer(dst *SecureTCPConn) error {
	log.Println(time.Now(),"目标地址：", dst.Address,"源地址：", secureSocket.Address)
	return secureSocket.EncodeCopy(dst)
}

```

现在运行，可以看到有日志的输出。

## 修改后代码可见[github](https://github.com/c-dafan/lightsocks)