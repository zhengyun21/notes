1. **c.SetCookie( )**
	1. `c.SetCookie(name, value string, maxAge int, path, domain string, secure, httpOnly bool)`
![[Pasted image 20260808112004.png]]
2. 示例
``` go
r.GET("/cookie", func(c *gin.Context) {  
    cookie, err := c.Cookie("testCookie") // 获取请求头中的Cookie: testCookie=???
    if err != nil {  
       fmt.Printf("testCookie = %v \n", cookie)  
       cookie = "newCookie" //设置cookie的值，请求头中Cookie: testCookie=newCookie
       fmt.Printf("testCookie = %v \n", cookie)  
       c.SetCookie("testCookie", cookie, 10, "/", "localhost", false, true) //10秒不过期
    } else {  
       fmt.Printf("testCookie = %v \n", cookie)  
    }  
    c.String(200, "cookie: "+cookie)  
})  
  
r.GET("/logout", func(c *gin.Context) {  
    cookie, _ := c.Cookie("testCookie") // 获取请求头中的Cookie: testCookie=???
    c.SetCookie("testCookie", cookie, -1, "/", "localhost", false, true)  // 设置 -1 让cookie过期，也就是删除。
    c.String(200, "succeed!")  
})
```
3. 通过 **http.Cookie** 设置 **cookie**
![[Pasted image 20260808112957.png]]
	1. `Expires` vs `MaxAge`
		两者都控制过期，但机制不同：
			- **`Expires`**：绝对时间点，依赖客户端时钟。老规范。
			- **`MaxAge`**：相对秒数，更可靠。现代浏览器优先使用它。
		删除 cookie 的标准做法：
			- `MaxAge = -1`，或
			- `Expires = time.Now().Add(-1 * time.Hour)`
	2. `SameSite` 取值
		- `Strict`：跨站请求完全不带 cookie
		- `Lax`：顶层导航的 GET 请求带（默认行为的事实标准）
		- `None`：跨站都带，但**必须同时设置 `Secure=true
	3. `MaxAge` 和 `Expires` 同时存在，优先`MaxAge`。
4. 示例
``` go
r.GET("/", func(c *gin.Context) {  
    c.SetCookieData(&http.Cookie{  
       Name:     "testCookie",  
       Value:    "newCookie",  
       Quoted:   true,  
       Path:     "/",  
       Domain:   "localhost",  
       Expires:  time.Now().Add(20 * time.Second),  
       MaxAge:   86400,  
       Secure:   true,  
       HttpOnly: true,  
       SameSite: http.SameSiteLaxMode,  
       // Partitioned: true, // Go 1.22+  
    })  
    c.JSON(200, "shezhi succeed")  
})
```
