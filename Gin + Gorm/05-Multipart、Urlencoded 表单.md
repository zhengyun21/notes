1. 使用 `c.PostForm()` 和 `c.DefaultPostForm()` 来读取表单提交的值。这些方法适用于 `application/x-www-form-urlencoded` 和 `multipart/form-data` 内容类型——这是浏览器提交表单数据的两种标准方式
2. 方式
	1. `c.PostForm("field")` 返回值，如果字段不存在则返回空字符串。
	2. `c.DefaultPostForm("field", "fallback")` 返回值，如果字段不存在则返回指定的默认值。
3. 示例
	1. ``` GO
		r.POST("/send_post", func(context *gin.Context) {  
		    name := context.PostForm("name")  
		    email := context.PostForm("email")  
		    message := context.DefaultPostForm("message", "nil")  
  
		    fmt.Printf("name: %s", name)  
		    fmt.Printf("email: %s", email)  
		    fmt.Printf("message: %s", message)  
  
		    context.JSON(200, gin.H{  
		       "message": "ok",  
		    })  
		})
	   ```