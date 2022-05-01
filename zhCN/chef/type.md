# Type 类型组件

Type 是 Chefs.go 内置组件，用于处理参数，对参数进行验证和包装。

## 组件定义

```
type Type struct {
    // Name 类型名称
    Name    string
    
    // Text 类型说明
	Text    string

    // Alias 类型别名
	Alias   []string

    // Valid 类型验证方法
	Valid   func(Any, Var) bool

    // Value 值包装方法
	Value   func(Any, Var) Any
}
```

## 注册例子

```golang
chef.Register("int", chef.Type{
    Alias: []string{ "int64" },     //可设置别名
    Name: "整型", Text: "整型",
	Valid: func(value Any, config Var) bool {
		switch v := value.(type) {
		case int, int32, int64, int8, float32, float64:
			return true
		case string:
			{
				v = strings.TrimSpace(v)
				if _, e := strconv.ParseInt(v, 10, 64); e == nil {
					return true
				}
			}
		}
		return false
	},
	Value: func(value Any, config Var) Any {
		switch v := value.(type) {
		case int:
			return int64(v)
		case int8:
			return int64(v)
		case int16:
			return int64(v)
		case int32:
			return int64(v)
		case int64:
			return int64(v)
		case float32:
			return int64(v)
		case float64:
			return int64(v)
		case string:
			{
				v = strings.TrimSpace(v)
				if i, e := strconv.ParseInt(v, 10, 64); e == nil {
					return i
				}
			}
		}

		return int64(0)
	},
})

```


## 使用方法

```
chef.Register(".article", http.Router{
	Uri: "/article/{uid}/{aid}",
	Args: Vars{
		"uid": Var{Type: "int", Required: true, Name: "编号"},
        "aid": Var{Type: "int64", Required: true, Name: "编号"},
	},
	Action: func(ctx *http.Context) {
		// 获取参数 uid
		uid := ctx.Args["uid"].(int64)
        // 获取参数 aid，类型为int64（别名）
        aid := ctx.Args["aid"].(int64)
	},
})
```

> 当你使用一个未定义的类型时，Chefs.go 会自动以注册过的 **正则表达式** 做为类型进行验证，并且将值包装成 **string** 类型。
