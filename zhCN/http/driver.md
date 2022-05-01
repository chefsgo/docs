# 动

模块是基于驱动化的设计，你可以很方便的编写自己的动。默认动基于开源项目 github.com/gorilla/mux ，感谢作者的贡献。

具体实现请参考http默认驱动：https://github.com/chefsgo/http-default

# Driver 驱动接口

```golang
type Driver interface {
	Connect(config Config) (Connect, error)
}
```

## Connect 连接接口

```golang
type Connect interface {
	// Open 打开连接
	Open() error
	// Health 运行状态
	// 返回驱动的运行状态信息，用于监控
	Health() (Health, error)
	//Close 关闭连接
	Close() error

	// Accept 委托
	Accept(Delegate) error
	// Register 注册路由
	Register(name string, info Info) error

	// Start 启动HTTP
	Start() error
	// StartTLS 以TLS的方式启动HTTP
	StartTLS(certFile, keyFile string) error
}
```

## Delegate 委托接口

```golang
type Delegate interface {
	// Serve 响应请求
	Serve(name string, params Map, res ResponseWriter, req *Request)
}
```

## Health 运行状态

```golang
type Health struct {
	// Workload 当前负载数
	Workload int64
}
```

## Info 注册信息

```golang
type Info struct {
	// Method 请求的METHOD
	// GET, POST 这些
	Method string
	// Uri 请求的Uri
	Uri string
	// Router 路由器名
	Router string
	// Site 对应的站点
	Site string
	// Hosts 绑定的域名
	Hosts []string
}
```
