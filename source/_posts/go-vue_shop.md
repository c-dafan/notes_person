---
title: shop_go_vue
date: 2019-04-05 10:44:58
tags: [go, vue, beego]
categories: Web
---

# 一个购物网站总结

一个购物网站，数据库设计如下：
![数据库](https://raw.githubusercontent.com/c-dafan/Shop_GoVueWeb/master/shop/static/shop.png)

前端nodejs + vue-cli + Vue + Element-UI

## vue

采用element-UI组件。记录一下体验：

        vue整体体验很好，通过router和router-view可以很好的提高前端布局代码的复用性。对我这种不擅长写前端，每次写前端ctrl+c，ctrl+v的真的是非常的友好。另外，数据的绑定、一些指令也是相当的好用
        使用vue-cli工具，需要借助nodejs，可以实现前后端的分离。在vue-cli的工程上，目录结构很清晰，很容易写一个前端。不过需要一些nodejs的知识，不然学起来真的很难受。
        element-UI组件很好使用，不用懂很多css也能写出来很漂亮页面，有种纯写html最后得到带css效果的页面。

笔记：

> 父子组件通信

* v-model 数据双向绑定

* v-on （@）事件

* v-bind （:） 数据单向绑定

> 内置指令

* v-if、v-else、v-else-if 条件

* v-for 循环 （o, i） in objs 其中i是索引，这个顺序好像和我见过的大部分语言相反

> 函数

* created

* watch 一直观察这个变量

* computed 一个变量通过函数结算出来

> slot、props、scope

* slot 插槽

* scope

* props

## beego

beego体验：

        beego从入门到放弃，没有重新运行解决不了的问题，beego基于mvc三层的一个框架，只要学过其他语言的WEB，入手还是很容易的。
        学习go语言一天，入手beego，本着练习go语言的原则，写了这个项目。
        beego有个bee工具，使用起来很容易。bee generate 几乎直接把真的网站的代码都写完了，需要改一些逻辑。生成的controller中GetAll也太恨了，把整个数据库的信息可以按照任意条件都能查询到。

项目中遇到的问题：

        通过注释配url，这个不知道是我的问题，还是beego的问题。配好的url，第一次不起作用，需要把进程关了，然后重新运行。第一次运行会生成一个文件，但是进入url会404，第二次运行就没问题。
        beego的orm也很用，很简单的就能和数据库映射。查询外键中的对象很可以查询到，不过一旦用了外键，外键对应的就成为了一个结构体，在发post等请求时，传外键Id时，需要多写一个{}。

beego笔记：

> bee

    generate    生成代码
    new         创建
    pack        打包
    run         运行

> orm

* qs.RelatedSel() 加载外键

        参数：要加载的外键名字，多层的话，可以使用__

* qs.Limit(limit, offset)

* struct

```go
type Address struct {
	Id             int    `orm:"column(aid);auto"`
	Uid            *User  `orm:"column(uid);null;rel(fk)"`
}
// auto自增、
// null可以为空、默认为非空、
// auto_now_add;type(datetime) 自动放到当时时间
// auto_now;type(datetime) 自动放到当时时间
// 区别auto_now_add第一次添加的时间，更新时间不变，auto_now每次更新时间改变
```

> Controller

* router

* ServeJSON