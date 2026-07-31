1. `ShouldBind` 会根据 HTTP 方法和 `Content-Type` 请求头自动选择绑定引擎：
	- 对于 **GET** 请求，使用查询字符串绑定（`form` 标签）。
	- 对于 **POST/PUT** 请求，它会检查 `Content-Type`——对 `application/json` 使用 JSON 绑定，对 `application/xml` 使用 XML 绑定，对 `application/x-www-form-urlencoded` 或 `multipart/form-data` 使用表单绑定。
	这意味着单个处理函数可以同时接受来自查询字符串和请求体的数据，无需手动选择数据源。
2. 示例
``` go
type Person struct {  
    Name     string    `form:"name"`  
    Address  string    `form:"address"`  
    BirthDay time.Time `form:"birthday" time_format:"2006-01-02" time_utc:"1"`  
}  
  
func startPage(c *gin.Context) {  
    var person Person  
    err := c.ShouldBind(&person)  
    if err != nil {  
       c.JSON(http.StatusBadRequest, gin.H{  
          "error": err.Error(),  
       })  
       return  
    }  
    fmt.Printf("Name：%s，Address：%s，Birthday：%s\n", person.Name,person.Address, person.BirthDay.Format("2006-01-02"))  
    c.JSON(http.StatusOK, gin.H{  
       "name":     person.Name,  
       "address":  person.Address,  
       "birthday": person.BirthDay,  
    })  
}

r.GET("/testing", startPage)  
r.POST("/testing", startPage)
```
