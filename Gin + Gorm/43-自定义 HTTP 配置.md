1. 默认情况下，`router.Run()` 启动一个基本的 HTTP 服务器。在生产环境中，你可能需要自定义超时、请求头限制或 TLS 设置。你可以通过创建自己的 `http.Server` 并将 Gin 路由器作为 `Handler` 传入来实现。
2. 示例
``` go
func main() {
	router := gin.Default()
	router.GET("/ping", func(c *gin.Context) {
		c.String(http.StatusOK, "pong")
	})
	http.ListenAndServe(":8080", router) // router.run() 其实就是调用这个。
} 
```
3. 使用自定义服务器设置
``` go
func main() {
	router := gin.Default()	
	router.GET("/ping", func(c *gin.Context) {	
		c.String(http.StatusOK, "pong")	
	})	
	s := &http.Server{	
		Addr: ":8080",	// 绑定端口号
		Handler: router, // 绑定路由	
		ReadTimeout: 10 * time.Second,	// 读取请求超时
		WriteTimeout: 10 * time.Second,	// 写请求超时
		MaxHeaderBytes: 1 << 20,	// 请求头最大1MB
	}	
	s.ListenAndServe()
}
```