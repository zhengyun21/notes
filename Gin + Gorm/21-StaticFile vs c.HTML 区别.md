1. `StaticFile` vs `c.HTML` 区别
	1. StaticFile（静态返回）
	```go
	func main() { 
		r := gin.Default() 
		// 直接把 index.html 文件内容返回给浏览器 
		// 即使 index.html 里有 {{.name}} 也只会原样显示 
		r.StaticFile("/", "index.html") r.Run(":8080") }
	```
	2. c.HTML（模板渲染）
	``` go
	func main() { 
		r := gin.Default() 
		r.LoadHTMLGlob("templates/*") // 必须先加载模板 
		r.GET("/", func(c *gin.Context) { 
		// 会解析 index.html 中的 {{.name}} 并替换为 "zzy" 
		c.HTML(200, "index.html", gin.H{ 
			"name": "zzy", 
			}) 
		}) 
		r.Run(":8080") 
	}
	```
2. 静态文件用 `StaticFile`，模板渲染用 `c.HTML`。