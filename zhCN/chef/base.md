# base 基础组件


base 基础组件，是 Chefs.go 最基础的组成部分，提供 Any, Map，Var，Res 等几个基本对象和一些常量的定义。

我们建议在引入此包的时候，直接引用到本地，如下所示：

```golang
import . "github.com/chefsgo/base"
```

然后在使用的时候，直接使用，不需要包名， 使代码更加的简洁美观。


```golang
import . "github.com/chefsgo/base"
func init() {
    var any Any
    var data Map
    var args Vars
    var res Res
}
```

而不是 


```golang
import "github.com/chefsgo/base"
func init() {
    var any base.Any
    var data base.Map
    var args base.Vars
    var res base.Res
}
```

## Any

Any 是指任意类型，在 Go 中表示为一个空接口 interface{}， Any 实际上是 interface{} 的别名。


> 注意，Go 1.18 已经内置了 any 对象，为了保持兼容性，Chefs.go 任然使用 Any。我们建议在项目中不要使用 any （小写）作为变量名，这样可以保证在 Go 1.18 及以后的版本中保持兼容。

定义如下：

```golang
type Any = interface{}
```

## Map

Map 对象相当于其它语言中的 HashMap，在 Go 对应的类型为 map[key]value，Map是指的拥有 string key 并 任意类型 value 的 map， Map 实际上是 map[string]interface{} 的别名，定义如下：

```golang
type Map = map[string]interface{}
```


## Vars

Vars 表示一个参数集合，用于定义参数表。定义如下：


```golang
type Vars = map[string]Var
```

## Var

Var 是一个用于定义参数的结构，用于定义各组件所需要的参数表，定义下如：

```golang
type Var struct {
	// nil 是否空参数，参考 Nil
	nil bool
	// Type 参数类型
	Type string
	// Required 是否必填，不可为空
	Required bool
	// Nullable 表示此参数可不存在
	// 为 true 时，解析参数的时候，如果为空，则不生成此参数，
	// 为 false 时，则生成默认值或nil
	Nullable bool
	// Name 参数名称
	Name string
	// Text 参数说明
	Text string
	// Default 默认值
	// 可以是常量，或是函数
	Default Any
	// Setting 参数配置，自定义配置
	// 一些特定的情况下，会有一些自定义的配置，
	Setting Map
	// Options 参数选项
	// 此参数不为空的时候，表示，值只允许是其中之一，相关于枚举选一
	// 注意，key为值，value为描述
	Options Map
	// Children 子参数表
	// 如此，就是支持无限下级的参数表
	Children Vars
	// Encode 编码方式
	// 具体参考 chef 内置的 codec 模块
	Encode string
	// Decode 解码方式
	// 具体参考 chef 内置的 codec 模块
	Decode string
	// Empty 自定义参数为空时的操作结果
	Empty Res
	// Error 自定义参数类型不对时的操作结果
	Error Res
	// Valid 自定义参数类型验证的方法
	Valid func(Any, Var) bool
	// Value 自定义参数通过验证后的值包装方法
	Value func(Any, Var) Any
}
```


## Nil

Nil 表示一个空的参数，有时候我们会使用现有定义好的参数表，但是会去掉其中一些参数，为了方便使用，就使用 Nil 来覆盖老的参数表。定义如下：

```golang
var Nil = Var{nil: true}
```

## Res

Res 是 Result 的简写，表示一个操作之后，返回的结果，它包含一个代码，一个文本串，以及可能的参数表，Chefs.go 整个框架会使用 Res 来代替 error 对象。

Res 被定义为一个接口，在 chef 中实现了这个接口， Res对象为空时或Code()==0，表示 成功，否则表示失败。

定义如下：

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


