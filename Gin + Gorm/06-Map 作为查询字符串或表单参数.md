1. 有时你需要接收一组事先不知道键名的键值对——例如动态过滤器或用户定义的元数据。Gin 提供了 `c.QueryMap` 和 `c.PostFormMap` 来将方括号表示法的参数（如 `ids[a]=1234`）解析为 `map[string]string`。
2. 方式
	1. `c.QueryMap("key")` —— 从 URL 查询字符串中解析 `key[subkey]=value` 形式的键值对。
	2. `c.PostFormMap("key")` —— 从请求体中解析 `key[subkey]=value` 形式的键值对。
3. 示例
	1. ``` go
		router.POST("/post", func(c *gin.Context) {
			ids := c.QueryMap("ids")
			names := c.PostFormMap("names")
			fmt.Printf("ids: %v; names: %v\n", ids, names)
			c.JSON(http.StatusOK, gin.H{
				"ids": ids,
				"names": names,
			})
		})
	   ```
4. 