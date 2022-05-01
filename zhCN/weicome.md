# 欢迎

Chefs.go 是一套用于后端开发的应用框架，拥有**极其灵活**、**简洁易用**的架构设计，适合用来开发各种后端应用。与绝大部分后端框架不同的是，Chefs.go 提供可直接上手、灵活可扩展、简洁易用、适用于任何规模的、完整的项目解决方案，不仅适合单体应用，也适合分布式项目。

Chefs.go 由一个核心和若干模块组成，灵活且模块化，驱动化的设计，让你无需修改一行代码便可轻松切换所接入的数据库、缓存库、消息系统等等。并且很容易就可以编写自定义的模块和驱动，无缝接入Chefs.go核心。

Chefs.go 实际上只提供了一个架子和一套规范，并且在这个架子的基础上提供了一些常用的模块、驱动、组件来帮助你快速完成你的项目。如果你觉得这些模块、驱动、组件不能满足你的需求，你完全可以在此基础之上，编写自己的模块、驱动、组件来替换掉官方的代码。当然也可以使用三方开源的兼容 Chefs.go 的模块、驱动和组件。

Chefs.go 的终极目标不是为了单进程的性能和跑分，而是为了统一的开发体验，更方便快速的开发和完成项目。当然，我们仍然可以优化代码以达到极致的性能，同时也可以分布式部署来无限扩容。

## 起步

> 无论你是新手还老手，你都可以很容易的使用 Chefs.go 来开发，我们假设你已经了解关于后端开发的基本知识，并且熟悉Golang语言，所以在本文档中，不会太过细节的解释基本知识和Golang的语法，如果你在这方面还不是太了解，建议先去了解相关知识。


首先，你需要下载并安装 Go 1.17 或以上版本，因为 Chefs.go 使用 go mod 来管理包。



下面是简单的Chefs.go应用Hello World，编写 **server.go** 文件：

```golang
package main

import (
    _ "github.com/chefsgo/builtin"
	"github.com/chefsgo/chef"
	"github.com/chefsgo/http"
)

func init() {
	chef.Register("index", http.Router{
		Uri: "/",
		Action: func(ctx *http.Context) {
			ctx.Text("Hello, World!")
		},
	})
}

func main() {
	chef.Go()
}
```


运行前，请先初始化 go mod 并拉取依赖包。
```
go mod init projectname
go mod tidy
```

运行运行你的代码

```
go run server.go
```

浏览器打开 http://127.0.0.1:8754，你应该就能看到

```
Hello, World!
```
如果不行，请检查你的Go版本是不是 v1.16 或以上版本，因为Chefs.go 使用 go mod 来管理包。







## chef 

```golang
func main() {
	chef.Go()
}
```

**chef** 是 Chefs.go 的核心，提供一个基础的架构，使各种模块可以方便接入到核心，启动后监听退出信号，使各模块和核心可以优雅的退出。




## http

```golang
import "github.com/chefsgo/http"
```

**http** 是 Chenfs.go 中的一个基本模块，提供 HTTP 服务，其中 http.Router 表示一个 HTTP路由器，具体细节请参考 HTTP模块。



## 注册机制

```golang
func init() {
    chef.Register("name", module.components...)
}
```

**注册机制** 是 Chefs.go 的核心之一，所有的模块、驱动、组件统一使用注册机制注册到核心，核心再自动注册到各个模块中。


```golang
import _ "github.com/chefsgo/builtin"
```

我们建议在包的 init 函数中注册内容，然后将对应的包，引入到您的项目中即可，上面的 **builtin** 就是一个例子，此包中内置了一些默认的驱动和方法，如你所知，Chefs.go 整个设计都是非常灵活的，你也可以使用自己的代码替换掉 builtin 中的代码。
