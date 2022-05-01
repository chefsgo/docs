# Codec 编解码组件

Codec 是 Chefs.go 内置组件，用于各种编码解码工作，比如项目中常用的 JSON, BASE64, XML, TOML, YAML, 再到常的对称加密算法，使用一个统一的组件，支持所有这些编解码。 Chefs.go 内置了（builtin包）一些，其它若有需要的，可以自行编写即可。

Codec 组件可以用在：配置解析、参数处理、请求解密、响应加密等各种场景中，也可以用在 队列系统、事件系统的编码解码，完美融入 Chefs.go 中的各种场景。

## 组件定义

```golang
Codec struct {
	// Name 名称
	Name string
	// Text 说明
	Text string
	// Alias 别名
	Alias []string
	// Encode 编码方法
	Encode func(v Any) (Any, error)
	// Decode 解码方法
	Decode func(d Any, v Any) (Any, error)
}
```

## 注册方式

```golang
chef.Register("base64", chef.Codec{
	Name:  "BASE64加解密", Text: "BASE64加解密",
	Encode: func(value Any) (Any, error) {
		var text string
		if vvs, ok := data.([]byte); ok {
			text = string(vvs)
		} else if vv, ok := data.(string); ok {
			text = vv
		} else {
			return errors.New("无效数据")
		}
		return base64.StdEncoding.EncodeToString([]byte(text)), nil
	},
	Decode: func(data Any, value Any) (Any, error) {
		text := anyToString(data)
		bytes, err := base64.URLEncoding.DecodeString(text)
		if err != nil {
			return "", err
		}
		return string(bytes), nil
	},
})

chef.Register("json", chef.Codec{
	Name: "JSON编解码", Text: "JSON编解码",
	Encode: func(value Any) (Any, error) {
		return json.Marshal(value)
	},
	Decode: func(data Any, value Any) (Any, error) {
		var bytes []byte
		if vvs, ok := data.([]byte); ok {
			bytes = vvs
		} else if vv, ok := data.(string); ok {
			bytes = []byte(vv)
		} else {
			return errors.New("无效数据")
		}

		err := jsonCoder.Unmarshal(bytes, value)
		if err != nil {
			return nil, err
		}
		return value, nil
	},
})
```


## 使用方法

直接在各种模块或是组件当中，加上Codec配置，如， config.Codec = "json" 即可。

## 参数系统中使用

```golang
chef.Register(".msg", http.Router{
	Uri: "/msg/{msg}",
	Args: Vars{
		"msg": Var{
			Type: "string", Required: true, Name: "消息",
			Decode: "base64",
		},
	},
	Action: func(ctx *http.Context) {
		// msg = Hello
		msg := ctx.Args["msg"].(string)
		ctx.Text(msg)
	},
})
```

访问 http://127.0.0.1:8754/msg/SGVsbG8= ，你将得到 **Hello**