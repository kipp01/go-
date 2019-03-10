### 功能特性

* 支持覆盖加载多个数据源（
  `[]byte`、文件和`io.ReadCloser`）
* 支持递归读取键值
* 支持读取父子分区
* 支持读取自增键名
* 支持读取多行的键值
* 支持大量辅助方法
* 支持在读取时直接转换为 Go 语言类型
* 支持读取和
  **写入**
  分区和键的注释
* 轻松操作分区、键值和注释
* 在保存文件时分区和键值会保持原有的顺序
* 用起来还挺方便

1，安装

```
go get github.com/go-ini/ini
```

2.获取配置文件

```
cfg,err := ini.Load("app.ini")
```

3.操作分区

```go
sec,err := cfg.GetSection("name")

获取默认分区可以使用空字符串代替
sec,err := cfg.GetSection("")

创建分区
err := cfg.NewSection("new section")

获取所有分区对象或名称：
secs := cfg.Sections()
names := cfg.SectionStrings()
读取父子分区
Cfg.Section("app").Key("RUN_MODE").String()

```



4.操作键

```go
获取某分区下的键
key,err := cfg.Section().Key("key name")
判断键存不存在
yes := cfg.Section("").HasKey("key name")
创建一个新的键：
err := cfg.Section("").NewKey("name", "value")

获取分区下的所有键或键名：
keys := cfg.Section("").Keys()
names := cfg.Section("").KeyStrings()
```

5.操作值

```
val := cfg.Section("").Key("key name").String()//可设置默认值
// 布尔值的规则：
// true 当值为：1, t, T, TRUE, true, True, YES, yes, Yes, y, ON, on, On
// false 当值为：0, f, F, FALSE, false, False, NO, no, No, n, OFF, off, Off
v, err = cfg.Section("").Key("BOOL").Bool()
v, err = cfg.Section("").Key("FLOAT64").Float64()
v, err = cfg.Section("").Key("INT").Int()
v, err = cfg.Section("").Key("INT64").Int64()
v, err = cfg.Section("").Key("UINT").Uint()
v, err = cfg.Section("").Key("UINT64").Uint64()
v, err = cfg.Section("").Key("TIME").TimeFormat(time.RFC3339)
v, err = cfg.Section("").Key("TIME").Time() // RFC3339

v = cfg.Section("").Key("BOOL").MustBool()
v = cfg.Section("").Key("FLOAT64").MustFloat64()
v = cfg.Section("").Key("INT").MustInt()
v = cfg.Section("").Key("INT64").MustInt64()
v = cfg.Section("").Key("UINT").MustUint()
v = cfg.Section("").Key("UINT64").MustUint64()
v = cfg.Section("").Key("TIME").MustTimeFormat(time.RFC3339)
v = cfg.Section("").Key("TIME").MustTime() // RFC3339

// 由 Must 开头的方法名允许接收一个相同类型的参数来作为默认值，
// 当键不存在或者转换失败时，则会直接返回该默认值。
// 但是，MustString 方法必须传递一个默认值。

v = cfg.Section("").Key("String").MustString("default")
v = cfg.Section("").Key("BOOL").MustBool(true)
v = cfg.Section("").Key("FLOAT64").MustFloat64(1.25)
v = cfg.Section("").Key("INT").MustInt(10)
v = cfg.Section("").Key("INT64").MustInt64(99)
v = cfg.Section("").Key("UINT").MustUint(3)
v = cfg.Section("").Key("UINT64").MustUint64(6)
v = cfg.Section("").Key("TIME").MustTimeFormat(time.RFC3339, time.Now())
v = cfg.Section("").Key("TIME").MustTime(time.Now()) // RFC3339

```



