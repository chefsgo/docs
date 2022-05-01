# result 结果组件

result 是 Chefs.go 内置组件，用于返回操作结果。result 实现了 Res 接口。并且逐步会使用 Res 替换现有代码中的 error。

result 提供统一的开发体验，状态码也可以明确表示错误的类型，方便在接口开发中使用，并且在多语言环境中，可以自动为用户显示不同语言的结果反馈。


## 为什么不直接使用 error ？

> 因为error只能返回一段文本，无法满足需求，我们除了需要错误信息外，还要状态码，还要可以携带参数。



## 组件定义

```golang
type result struct {
	// code 状态码
	// 0 表示成功，其它表示失败
	code int
	// state 对应的状态
	state string
	//携带的参数
	args []Any
}
```


## Res 接口定义

```golang
type Res interface {
	// OK 函数返回操作是否成功，Res 对象为空，或是 Code() 为 0，表示成功，其它均表示失败。
	OK() bool
	// Fail 函数返回操作是否失败，与 OK() 正好相反。
	Fail() bool
	// Code 表示返回的状态码，其中 0 表示成功，其它均表示失败。
	Code() int
	// State 表示结果中的原始状态信息
	State() string
	// Args 表示Res所携带的参数。
	Args() []Any
	// With 方法，使用新的参数，生成一个新的 Res对象
	// 统常在需要返回动态结果的时候使用
	With(args ...Any) Res
	// Error 为兼容 error 对象的方法
	Error() string
}
```


## 注册例子

```golang
var LoginOK			= chef.Result(0, "login.ok", "登录成功“)
var LoginPassword	= chef.Result(1001, "login.password", "密码错误")
var LoginFail 		= chef.Result(1002, "login.fail", "登录失败“)
```


## 使用方法

```golang
func Login() Res {
	if xxyyzz {
		return LoginFail
	} else if password {
		return LoginPassword
	}
	return LoginOK
}

res := Login()
if res.Fail() {
	//失败了，返回提示
} else {
	//登录成功，做成功后的操作
}

```

## 携带参数

```golang
var ArgsOK		= chef.Result(0, "args.ok", "成功")
var ArgsInvalid = chef.Result(100, "args.invalid", "参数%s的%s无效“)

func Test(args string) Res {
	if args..... {
		return ArgsInvalid.With("参数名", "类型")
	}
	return ArgsOK
}
```


## 状态码说明

Chefs.go 框架以及各个模块会使用 **0** 和 **小于0** 的状态码，做为内置的状态码，**0** 表示成功，**负数** 表示失败。并且默认情况下每一个模块分配 1000 个状态码（不够用的情况下可以以1000为单位递增），比如：

> chef 核心使用 -1 ~ -999 做为状态码<br/>
> log 模块使用 -1000 ~ -1999 做为状态码<br/>
> http 模块使用 -2000 ~ -2999 做为状态码<br/>
> 以此类推

建议你在项目中也可以按 1000 为1个单位来分配状态码，比如：

> 全局使用 1 ~ 999 做为状态码<br/>
> user 模块使用 1000 ~ 1999 做为状态码<br/>
> 以此类推