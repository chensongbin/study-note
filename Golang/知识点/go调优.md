# tools

## pprof

```go
package main
import (
  "net/http"
    _ "net/http/pprof"
)

func main() {
    http.ListenAndServe("0.0.0.0:6060", nil)
}
```





## trace









线上环境实战：

```go
package main

import (
	// 略
	_ "net/http/pprof" // 会自动注册 handler 到 http server，方便通过 http 接口获取程序运行采样报告
	// 略
)

func main() {
	// 略

	runtime.GOMAXPROCS(1) // 限制 CPU 使用数，避免过载
	runtime.SetMutexProfileFraction(1) // 开启对锁调用的跟踪
	runtime.SetBlockProfileRate(1) // 开启对阻塞操作的跟踪

	go func() {
		// 启动一个 http server，注意 pprof 相关的 handler 已经自动注册过了
		if err := http.ListenAndServe(":6060", nil); err != nil {
			log.Fatal(err)
		}
		os.Exit(0)
	}()

	// 略
}
```



https://blog.wolfogre.com/posts/go-ppof-practice/

