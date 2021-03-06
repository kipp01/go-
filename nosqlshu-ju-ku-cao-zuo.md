#### redis

redis是一个key-value存储系统。和memcachedl类似，它支持存储的值类型相对更多，包括string，list，set，zset

Go 目前支持 redis 的驱动有如下

* [https://github.com/garyburd/redigo](https://github.com/garyburd/redigo)
  \(推荐\)
* [https://github.com/go-redis/redis](https://github.com/go-redis/redis)
* [https://github.com/hoisie/redis](https://github.com/hoisie/redis)
* [https://github.com/alphazero/Go-Redis](https://github.com/alphazero/Go-Redis)
* [https://github.com/simonz05/godis](https://github.com/simonz05/godis)

github.com/garyburd/redigo/redis驱动操作

```go
package main

import (
	"fmt"
	"github.com/garyburd/redigo/redis"
	"log"
	"os"
	"os/signal"
	"syscall"
	"time"
)

var(
	Pool *redis.Pool
)
func init(){
	redisHost := ":6379"
	Pool = newPool(redisHost)
	close()
}

func newPool(server string)*redis.Pool{
	return &redis.Pool{
		MaxIdle:3,
		IdleTimeout:240*time.Second,
		Dial: func() (conn redis.Conn, e error) {
			c,err := redis.Dial("tcp",server)
			if err != nil {
				return nil,err
			}
			return c,err
		},
		TestOnBorrow: func(c redis.Conn, t time.Time) error {
			_,err := c.Do("PING")
			return  err
		},
	}
}

func close(){
	c := make(chan os.Signal,1)
	signal.Notify(c,os.Interrupt)
	signal.Notify(c,syscall.SIGTERM)
	signal.Notify(c,syscall.SIGKILL)
	go func() {
		<-c
		Pool.Close()
		os.Exit(0)
	}()
}


func Get(key string)([]byte,error){
	conn := Pool.Get()
	defer conn.Close()
	var data []byte
	data,err := redis.Bytes(conn.Do("GET",key))
	if err != nil {
		return data,fmt.Errorf("error get key %s: %v", key, err)
	}
	return data,err
}

func Set(key,value string)(error){
	conn := Pool.Get()
	defer conn.Close()
	if n,err :=conn.Do("SET",key,value); n != "OK"{
		return err
	}
	return nil
}



func main() {
	err := Set("test","123")
	if err != nil {
		log.Fatalf("set key err:%v",err)
	}
	test,err := Get("test")
	fmt.Println(string(test),err)
}

```

 github.com/astaxie/goredis 驱动操作

```go
package main

import (
	"fmt"
	"github.com/astaxie/goredis"
)

func main() {
	var client goredis.Client
	client.Addr = "127.0.0.1:6379"
	//字符串
	client.Set("a",[]byte("hello"))
	val,_ := client.Get("a")
	fmt.Println(string(val))
	client.Del("a")
	//list链表操作
	vals := []string{"a","b","c","d","e"}
	for _,val := range vals {
		client.Rpush("1",[]byte(val))
	}
	dbval,_ := client.Lrange("1",0,4)
	for i,v := range dbval  {
		fmt.Println(i,":",string(v))
	}
	client.Del("1")
	}

```

 

