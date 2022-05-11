# http配置

http 模块拥有 **2** 个配置，一个是http本身的配置，另一个是 Site（站点）的配置，http模块在监听1个端口的情况下支持多个站点。


## 如何配置

http 兼容 chef 核心的 Module 接口，统一使用 chef.Register 来注册配置，或，使用 chef.Configure 来加载动态配置。


## 使用注册中心注册

使用 chef.Register 直接注册 http.Config 或 http.Site 即可，可以分多次调用分多次配置。

> 注意：后加载的配置会覆盖之前的配置。

```golang
chef.Register(http.Config{
    ......具体参数见下表......
})
chef.Register("www", http.Site{
    ......具体参数见下表......
})
chef.Register(http.Sites{
    "www": http.Site{
        ......具体参数见下表......
    },
    "api": http.Site{
        ......具体参数见下表......
    },
})
```



## 使用动态配置

http 动态配置处理 http, site, sites 几个字段，会动态处理覆盖现有配置中的字段（不会整个覆盖）。

chef 核心框架会在启动之前，自动从配置文件、环境变量、命令行参数加载动态配置，如果设置了相应的配置，外部加载的配置会覆盖现有的字段（不会整个覆盖）。

所以，在代码中设置的所有配置，可以理解为给模块设置的默认配置，可以在开发的时候，减小依赖，而项目实际运行中一般都是需要加载外部配置。

```golang
chef.Configure(Map{
    "http": Map{
        "driver": "default", "port": 8754, "charset": "utf-8",
    },
    "site": Map{
        "www":Map{
            "name": "主网站",
        }
    },
})
```

## http配置表

```golang
type http.Config struct {
	// Driver 驱动
	Driver string
	// Port 监听端口
	Port int

	// CertFile SSL证书cert文件
	CertFile string
	// Key SSL证书key文件
	KeyFile string

	// Domain 默认域，是主域，不是子域名，如 chefsgo.org
	Domain string

	// Charset 默认字符集
	// 默认值 utf
	Charset string

	// Cookie 默认cookie名称
	// 用于token或sessionid的浏览器cookie名
	Cookie string

	// MaxAge cookie的超时时间
	// 0  表示不过时
	MaxAge time.Duration
	// HttpOnly COOKIE设置是否httponly
	HttpOnly bool

	// Expiry 老代码遗留，暂时不用
	Expiry time.Duration

	// Upload 上传文件临时目录
	// 默认 os.TempDir()
	Upload string
	// Static 静态文件目录
	// 默认 asset/statics
	Static string
	// Shared 默认共享目录
	// 静态文件搜索的共享目录名
	// 默认值 shared
	Shared string

	// Defaults 默认文件名
    // 当访问静态文件时，如果目录的是目录，默认搜索的文件名
	// 默认 index.html default.html
	Defaults []string

	Setting Map
}
```

## Site配置表


```golang
Site struct {
	// Name 名称
	Name string

	// Ssl 是否开启SSL
	Ssl bool

	// Domain 主域
	// 为空则从 http.Config 中继承
	Domain string

	// Hosts 绑定的域名列表
	// 如果为空，为自动设置为当前站点的 key + Domain
	// 如果Hosts中带主域名，比如，只设置为 ["aaa", "bbb"]
	// 系统会自动将 aaa,bbb 加上 主域名，如 aaa.chefsgo.org
	Hosts []string

	// Charset 字符集
	// 为空则从 http.Config 中继承，默认 utf-8
	Charset string

	// Cookie 默认cookie名称
	// 用于token或sessionid的浏览器cookie名
	Cookie string

	// MaxAge cookie的超时时间
	// 0  表示不过时
	MaxAge time.Duration
	// HttpOnly COOKIE设置是否httponly
	HttpOnly bool

	// Expiry 老代码遗留，暂时不用
	Expiry time.Duration

	// Setting 设置
	Setting Map
}
```