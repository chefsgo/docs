# Mimetype 组件

Mimetype 是 Chefs.go 内置组件，用于定义MIMEType，方便在业务使用。

Mimetype 主要在 http 模块中使用，比如 返回文件时，自动根据文件扩展名 获取 MIMEType。

## 组件定义

```
type Mime []string
type Mimes map[string][]string
```

## 注册方式
```golang
chef.Register("mp4", chef.Mime{
	"video/mp4",
})
chef.Register(chef.Mimes{
	"mp4": chef.Mime{"video/mp4"},
	"apk": chef.Mime{
		"application/vnd.android.package-archive",
		"application/vnd.android",
	},
})
```


## 使用方法

```golang
// 按mime获取扩展名
// ext1 = mp4,
// ext2 = def
ext1 := chef.Extension("video/mp4")
ext2 := chef.Extension("xxxxx/mp4", "def")

// 按扩展名获取mimetype
// mime1 = video/mp4
// mime2 = def/def
mime1 := chef.Mimetype("mp4")
mime2 := chef.Mimetype("mp5", "def/def")
```
