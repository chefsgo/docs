# Language 多语言 组件

Language 是 Chefs.go 内置组件，用于定义多语言以及对应的字串集，方便实现国际化。

## 组件定义

```golang
Strings  map[string]string
Language struct {
	// Name 语言名称
	Name string
	// Text 语言说明
	Text string
	// Accepts 匹配的语言
	// 比如，znCN, zh, zh-CN 等自动匹配
	Accepts []string
	// Strings 当前语言是字符串列表
	Strings Strings
}
```

## 注册方式

```golang
chef.Register("zhCN", chef.Language{
	Name:    "简体中文", Text: "简体中文",
	Accepts: []string{"zh", "zhCN", "zh-cn"},
	Strings: chef.Strings{
		"login.ok":   "登录成功",
		"login.fail": "登录失败",
	},
})
chef.Register("en", chef.Language{
	Name:    "English", Text: "English",
	Accepts: []string{"en", "enUs", "en-US"},
	Strings: chef.Strings{
		"login.ok":   "Login successful",
		"login.fail": "Login failed",
	},
})

//也可以直接注册多语言字串
chef.Register("zhCN", chef.Strings{
	"login.ok":   "登录成功",
	"login.fail": "登录失败",
})
chef.Register("en", chef.Strings{
	"login.ok":   "Login successful",
	"login.fail": "Login failed",
})
```


## 使用方法

```golang
// 获取语言字段
// str1 = 登录成功
// str2 = Login successful
str1 := chef.String("zhCN", "login.ok")
str2 := chef.String("en", "login.ok")
```

## 在上下文中使用

```golang
chef.Register(".index", http.Router{
	Uri: "/",
	Action: func(ctx *http.Context) {
		// 设置当前上下文的语言为 zhCN
		ctx.Language("zhCN")
		// str1 = 登录成功
		str1 := ctx.String("login.ok")

		// 设置当前上下文的语言为 en
		ctx.Language("en")
		// str = Login successful
		str2 := ctx.String("login.ok")
	},
})
```

> http上下文会自动根据请求的 Accept-Languages 处理，和语言定义中的 Accepts 相匹配，自动设置语言，如果没有匹配上，ctx.Language() 将被设置为 “default”。若想要处理上下文的语言，可以使用缓存或是数据库来保存用户当前的语言，然后在**拦截器**中设置即可。



## 在 view 层使用

```html
{%string `login.ok`%}
```