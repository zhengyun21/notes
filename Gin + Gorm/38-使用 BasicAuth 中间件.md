1. Gin 内置了 `gin.BasicAuth()` 中间件，实现了 [HTTP 基本认证](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#basic_authentication_scheme)。它接受一个 `gin.Accounts` 映射（`map[string]string` 的快捷方式），包含用户名/密码对，并保护应用它的任何路由组。
2. 示例
``` go
// 这里定义了一个 map （gin.H 就是 map 的别名）
var secrets = gin.H{  
    "foo": gin.H{  
       "email": "foo@bar.com",  
       "phone": "123433",  
    },  
    "austin": gin.H{  
       "email": "austin@example.com",  
       "phone": "666",  
    },  
    "lena": gin.H{  
       "email": "lena@guapa.com",  
       "phone": "523443",  
    },  
}  
  
func main() {  
    r := gin.Default()  
    r.StaticFile("/", "index.html")  
  
    Authorization := r.Group("/admin", gin.BasicAuth(gin.Accounts{  
       "foo":    "bar",  
       "austin": "1234",  
       "lena":   "hello2",  
       "manu":   "4321",  
    }))

    Authorization.GET("/secrets", func(c *gin.Context) {  
       user := c.MustGet(gin.AuthUserKey).(string)  
       // 等价于：user := c.MustGet("user").(string)
       secret, ok := secrets[user]  
       if ok {  
          c.JSON(http.StatusOK, gin.H{"user": user, "secret": secret})  
       } else {  
          c.JSON(http.StatusOK, gin.H{"user": user, "secret": "NO SECRET :("})  
       }  
    })  
  
    r.Run(":8081")  
}
```
3. 上面代码在前端输入用户名和密码之后的执行流程
![[Pasted image 20260805180231.png]]