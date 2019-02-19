## mongoDB

  
MongoDB 是一个高性能，开源，无模式的文档型数据库，是一个介于关系数据库和非关系数据库之间的产品，是非关系数据库当中功能最丰富，最像关系数据库的。他支持的数据结构非常松散，采用的是类似 json 的 bjson 格式来存储数据，因此可以存储比较复杂的数据类型。Mongo 最大的特点是他支持的查询语言非常强大，其语法有点类似于面向对象的查询语言，几乎可以实现类似关系数据库单表查询的绝大部分功能，而且还支持对数据建立索引。

下图展示了 mysql 和 mongoDB 之间的对应关系，我们可以看出来非常的方便，但是 mongoDB 的性能非常好。

[![](https://golangcaffcdn.phphub.org/build-web-application-with-golang/images/5.6.mongodb.png?raw=true)](https://golangcaffcdn.phphub.org/build-web-application-with-golang/images/5.6.mongodb.png?raw=true)

图 5.1 MongoDB 和 Mysql 的操作对比图

目前 Go 支持 mongoDB 最好的驱动就是[mgo](http://labix.org/mgo)，这个驱动目前最有可能成为官方的 pkg。

安装 mgo和下面我将演示如何通过 Go 来操作 mongoDB：



```
go get gopkg.in/mgo.v2

```

```
package main

import (
    "fmt"
    "log"

    "gopkg.in/mgo.v2"
    "gopkg.in/mgo.v2/bson"
)

type Person struct {
    Name  string
    Phone string
}

func main() {
    session, err := mgo.Dial("server1.example.com,server2.example.com")
    if err != nil {
        panic(err)
    }
    defer session.Close()

    // Optional. Switch the session to a monotonic behavior.
    session.SetMode(mgo.Monotonic, true)

    c := session.DB("test").C("people")
    err = c.Insert(&Person{"Ale", "+55 53 8116 9639"},
        &Person{"Cla", "+55 53 8402 8510"})
    if err != nil {
        log.Fatal(err)
    }

    result := Person{}
    err = c.Find(bson.M{"name": "Ale"}).One(&result)
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("Phone:", result.Phone)
}
```



