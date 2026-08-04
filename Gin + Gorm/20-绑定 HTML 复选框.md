1. 具有相同 `name` 属性的 HTML 复选框在被选中时会提交多个值。Gin 可以通过使用带有 `[]` 后缀的 `form` 结构体标签（匹配 HTML 的 name 属性）将这些值直接绑定到结构体的 `[]string` 切片中。
2. 示例
``` go
type multiForm struct {  
    Name    string   `form:"name"`  
    Hobbies []string `form:"hobbies[]"`  
}

r.POST("/hobby", func(c *gin.Context) {  
    var form2 multiForm  
    err := c.ShouldBind(&form2)  
    if err != nil {  
       c.String(400, err.Error())  
    }  
    fmt.Printf("name: %v  form: %v \n", form2.Name, form2.Hobbies)  
    c.String(200, "ok")  
})
```
3. `colors[]` 中的 `[]` 后缀是 HTML 的约定，不是 Go 的要求。结构体标签必须与 HTML 的 `name` 属性完全匹配。如果你的 HTML 使用 `name="colors"`（不带方括号），你的结构体标签应该是 `form:"colors"`。