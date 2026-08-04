1. `ShouldBind` 会自动检测 `Content-Type`，并将 `multipart/form-data` 或 `application/x-www-form-urlencoded` 请求体绑定到结构体中。使用 `form` 结构体标签将表单字段名映射到结构体字段，使用 `binding:"required"` 来强制必填字段。这通常用于登录表单、注册页面或任何 HTML 表单提交。
2. 示例
``` go
type LoginForm struct {
	User string `form:"user" binding:"required"`
	Password string `form:"password" binding:"required"`
}
func main() {
	r := gin.Default()
	r.POST("/login", func(c *gin.Context) {
		var form LoginForm
		// ShouldBind automatically selects the right binding based on Content-Type
		if err := c.ShouldBind(&form); err != nil {
			c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
			return // 注意要return，防止继续往下走分支
		}
		if form.User == "user" && form.Password == "password" {
			c.JSON(http.StatusOK, gin.H{"status": "you are logged in"})
		} else {
			c.JSON(http.StatusUnauthorized, gin.H{"status": "unauthorized"})
		}
	})
	router.Run(":8080")
}
```