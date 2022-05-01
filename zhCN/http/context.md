# Context 上下文  方法

http 请求的执行线类似于 “洋葱” 一样的设计，每个请求都会有一例会的方法被执行，其中包括拦截器，路由方法，处理器等等。 Context 贯穿整个执行过程，请求和响应都包装在了 Context 上下文中。

> 你在上下文中（ctx）做的所有操作，都不会真正发送回客户端，只有 Response阶段 才会真正的发送内容回客户端。

## 使用方式

在所有组件（Filter拦截器, Router路由器, Handler处理器）中，都统一使用  Context（上下文）

```golang
func(ctx *http.Context) {
	//在这里使用
}
```

## ctx.Next()

上下文中最重要的方法，表示进入下一个方法继续执行，如果不调用此方法，则当前上下文直接中断执行，立即进入 Response响应阶段。
ctx.Next通常只在拦截器中执行，这也是在Filter拦截器中最常使用的方法，表示继续继续往下执行。

```golang
// 不符合条件，直接中断执行，程序立即进入 
if xxxyyyzzz {
    return
}
ctx.Next()
```




## ctx.Output(Res, datas...) 

返回一个接口输出，包括一个操作结果，和返回的数据。

`http模块重点设计了对接口的响应，所有接口统一格式返回内容，格式如下：`

```json
{ "code": 0, "text": "提示信息", "time": 1651424905, "data": {} }
```

其中各字段说明：

- code 状态码，0表示成功，其它都是失败
- text 提示信息，可为空，如不为空，客户端应该弹出提示信息
- time 时间戳，服务器当前时间
- data 数据，可为空，操作失败一般不返回数据


```golang
//返回状态
ctx.Output(chef.OK)
// {"code":0, "text": "成功", "time": 1651424905}

//只返回数据
ctx.Outpu(nil, Map{"msg": "返回数据"})
// {"code":0, "time": 1651424905, "data": { "msg": "返回数据" }}

//状态+数据
ctx.Outpu(chef.OK, Map{ "msg": "返回数据"})
// {"code":0, "text": "成功", "time": 1651424905, "data": { "msg": "返回数据" }}
```














## ctx.Metadata(args...)

**获取** 或 **设置** 当前上下文的元数据，至于什么是元数据，请参考 元&数据 文档。
通常在项目开发的时候不太需要使用到 元&数据 的功能，只有在开发模块的时候会用到。

```golang
md := ctx.Metadata()
ctx.Metadata(md)
```





## ctx.Language(args...) 

**获取** 或 **设置** 当前上下文的语言，

```golang
lang := ctx.Language()
ctx.Language("zh-CN")
```




## ctx.Timezone(args...) 

**获取** 或 **设置** 当前上下文的时区，

```golang
zone := ctx.Timezone()
ctx.Timezone(time.Local)
```





## ctx.Result(args...) 

**获取** 或 **设置** 最后的Result（调用返回的结果），如果结果为空，则默认返回 chef.OK 表示成功。
设置操作结果，如果你操作了某个操作，返回了一个结果，你可以将结果设置到 Context 中去，方便拦截器，处理器上去使用它。

```golang
// res 默认 = chef.OK
res := ctx.Result()
ctx.Result(chef.Fail)
```

## ctx.String(key, args...)

获取多语言字串。根据当前 上下文 的语言，来获取指定的字串定义。
```golang
// str = 登录成功
str := ctx.String("login.ok")
```






## ctx.Charset(args...) 

**获取** 或 **设置** 当前上下文的字符集。

```golang
cs := ctx.Charset()
ctx.Charset("utf-8")
```




## ctx.Header(key, args...) 

**获取** 或 **设置** HTTP Header 响应头。

> 注意：此方法不会实时写入 HTTP Header，而是在 **Response阶段** 统一写入，在执行过程中可被修改。

```golang
ua := ctx.Header("User-Agent")
ctx.Header("HeaderKey", "HeaderValue")
```



## ctx.Cookie(key, args...) 

**获取** 或 **设置** HTTP Cookie 。

> 注意：此方法不会实时写入 HTTP Cookie，而是在 **Response阶段** 统一写入，在执行过程中可被修改。

```golang
ua := ctx.Cookie("User-Agent")
ctx.Cookie("CookieKey", "CookieValue")
ctx.Cookie("CookieKey", http.Cookie{})
```





## ctx.Goto(url) 

返回一个跳转

> 注意：此方法不会实时返回，而是在 **Response阶段** 统一返回。

```golang
ctx.Goto("https://chefsgo.org")
ctx.Redirect("https://chefsgo.org")
```






## ctx.Text(text, args...) 

返回一段文本，默认类型 text，默认状态 200

> 注意：此方法不会实时返回，而是在 **Response阶段** 统一返回。

```golang
// 返回文本
ctx.Text("text")
// 文本带HTTP状态
ctx.Text("text", http.StatusOK)
// 文本带类型
ctx.Text("text", "xml")
// 文本带状态和类型
ctx.Text("text", "xml", http.StatusOK)
// 后面2个参数可以换位置
ctx.Text("text", http.StatusOK, "xml")
```






## ctx.HTML(html, args...) 

返回一段HTML，默认类型 html，默认状态 200

> html类型，可以注册 chef.Mime 替换，默认为 text/html

```golang
ctx.HTML("html")
// 带HTTP状态
ctx.HTML("html", http.StatusOK)
// 带类型
ctx.HTML("html", "xml")
// 带状态和类型
ctx.HTML("html", "xml", http.StatusOK)
// 后面2个参数可以换位置
ctx.HTML("html", http.StatusOK, "xml")
```




## ctx.Script(script, args...) 

返回一段js脚本，默认类型 script，默认状态 200

> script类型，可以注册 chef.Mime 替换，默认为 text/script

```golang
ctx.Script("<script>")
// 带HTTP状态
ctx.Script("<script>", http.StatusOK)
// 带类型
ctx.Script("<script>", "html")
// 带状态和类型
ctx.Script("<script>", "html", http.StatusOK)
// 后面2个参数可以换位置
ctx.Script("<script>", http.StatusOK, "html")
```



## ctx.JSON(json, args...) 

返回一段HTML，默认类型 json，默认状态 200

> json类型，可以注册 chef.Mime 替换，默认为 text/json

```golang
ctx.JSON(jsonObj)
// 带HTTP状态
ctx.JSON(jsonObj, http.StatusOK)
// 带类型
ctx.JSON(jsonObj, "json2")
// 带状态和类型
ctx.JSON(jsonObj, "json2", http.StatusOK)
// 后面2个参数可以换位置
ctx.JSON(jsonObj, http.StatusOK, "json2")
```



## ctx.JSONP(callback, jsonObj, args...) 

返回一段HTML，默认类型 html，默认状态 200

> html类型，可以注册 chef.Mime 替换，默认为 text/script

```golang
ctx.JSONP(callback, jsonObj)
// 带HTTP状态
ctx.JSONP(callback, jsonObj, http.StatusOK)
// 带类型
ctx.JSONP(callback, jsonObj, "json2")
// 带状态和类型
ctx.JSONP(callback, jsonObj, "json2", http.StatusOK)
// 后面2个参数可以换位置
ctx.JSONP(callback, jsonObj, http.StatusOK, "json2")
```




## ctx.XML(json, args...) 

返回一段XML，默认类型 xml， 状态 200

> xml类型，可以注册 chef.Mime 替换，默认为 text/xml

```golang
ctx.XML(xmlObj)
// 带HTTP状态
ctx.XML(xmlObj, http.StatusOK)
// 带类型
ctx.XML(xmlObj, "json2")
// 带状态和类型
ctx.XML(xmlObj, "json2", http.StatusOK)
// 后面2个参数可以换位置
ctx.XML(xmlObj, http.StatusOK, "json2")
```


## ctx.File(file, args...) 

返回一个文件，默认类型 file, 状态 200

> file类型，可以注册 chef.Mime 替换，默认为 application/octet-stream

```golang
ctx.File(file)
ctx.File(file, "type")
ctx.File(file, "type", “自定义下载文件名”)
```


## ctx.Buffer(io.ReadCloser, args...) 

返回一个Buffer，默认类型 file, 状态 200

> file类型，可以注册 chef.Mime 替换，默认为 application/octet-stream

```golang
ctx.Buffer(ReadCloser)
ctx.Buffer(ReadCloser, "type")
ctx.Buffer(ReadCloser, "type", “自定义下载文件名”)
```



## ctx.Binary([]byte, args...) 

返回字符数组做为文件，默认类型 file, 状态 200

> file类型，可以注册 chef.Mime 替换，默认为 application/octet-stream

```golang
ctx.Binary(bytes)
ctx.Binary(bytes, "type")
ctx.Binary(bytes, "type", “自定义下载文件名”)
```



## ctx.View(view, args...) 

返回一个视图文件，默认类型 html, 状态 200

> html类型，可以注册 chef.Mime 替换，默认为 text/html

```golang
//写入 ViewData
ctx.Data["msg"] = "viewdata"

ctx.View("index")
//返回  view Model
ctx.View("index", Map{ "msg": "msg" })
//自定义类型
ctx.View("index", "xml")
//自定义类型+状态
ctx.View("index", "xml", http.StatusOK)
```


## ctx.Alert(Res, urls...) 

返回一个JS的弹框，并支持跳转的URL

```golang
ctx.Alert(chef.OK)
ctx.Alert(chef.OK, "https://chefsgo.org")
```




## ctx.Show(Res, urls...) 

返回一个专门的提示页面，并支持跳转的URL。

需要自己在views目录中建一个 show.html 的视图文件，数据由 ctx.Data["show"] 来传递

格式为 { code, text, url }，其中 url 可为空

```golang
ctx.Show(chef.OK)
ctx.Show(chef.OK, "https://chefsgo.org")
```



## ctx.Status(code, texts...) 

直接返回一个HTTP状态，并可以带一段文本

```golang
ctx.Status(http.StatusOK)
ctx.Status(http.StatusOK, "请求成功")
```


