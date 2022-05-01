# State 状态 组件

State 是 Chefs.go 内置组件，用于定义状态和对应的状态码，一般不单独使用，需要结果 Res （Result）使用，Result定义会自动注册状态和状态码，并注册默认的多语言字串。

## 组件定义

```golang
type State  int
type States map[string]State
```

## 注册方式

```golang
chef.Register("send.ok", chef.State(0))
chef.Register(chef.States{
	"login.ok":   0,
	"login.fail": 1,
})
```


> 请参考 result 组件
