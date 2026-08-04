1. `uri` 绑定用于把 **URL 路径中的参数**（即路由占位符 `:xxx` 匹配到的值）绑定到结构体字段，专门配合 `ShouldBindUri` 使用。
2. 示例
``` GO
type Person struct {
	ID string `uri:"id" binding:"required,uuid"` //id必须为uuid合法格式，否则报错
	Name string `uri:"name" binding:"required"`
}

func main() {
	route := gin.Default()
	route.GET("/:name/:id", func(c *gin.Context) {
	var person Person
	if err := c.ShouldBindUri(&person); err != nil {
		c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
		return
	}
	c.JSON(http.StatusOK, gin.H{"name": person.Name, "uuid": person.ID})
	})
	route.Run(":8088")
}
```