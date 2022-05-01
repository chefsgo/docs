# Regular 正则表达式 组件

Regular 是 Chefs.go 内置组件，用于定义正则表达式，方便在业务使用。

## 组件定义

```golang
type Regular  []string
type Regulars map[string]Regular
```

## 注册方式

```golang
chef.Register("email", http.Regular{
	`^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+((\.[a-zA-Z0-9_-]{2,3}){1,2})$`,
})
chef.Register(chef.Regulars{
	"mobile": chef.Regular{`^1[0-9]{10}$`},
	"date": chef.Regular{
		`^(\d{4})(\d{2})(\d{2})$`,
		`^(\d{4})-(\d{2})-(\d{2})$`,
	},
})
```


## 使用方法

```golang
email := "docs@chefsgo.org"
if chef.Match("email", email) {
	//通过验证
} else {
	//未通过
}
```


## 在参数中使用

```golang
chef.Register(".login.email", http.Router{
	Uri: "/login/email",
	Args: Vars{
		"email": Var{Type: "email", Required: true, Name: "电子邮箱"},
        "password": Var{Type: "string", Required: true, Name: "密码"},
	},
	Action: func(ctx *http.Context) {
		// 获取参数
		email := ctx.Args["email"].(string)
        password := ctx.Args["password"].(string)
	},
})
```

> 正则表达式做为类型使用时，值会包装成 **string** 类型。
