1. Go 的 `json.Marshal` 会出于安全考虑将特殊 HTML 字符替换为 Unicode 转义序列——例如 `<` 变成 `\u003c`。将 JSON 嵌入 HTML 时这很好，但如果你正在构建纯 API，客户端可能期望得到原始字符。
2. `c.PureJSON` 使用 `json.Encoder` 并设置 `SetEscapeHTML(false)`，因此 `<`、`>` 和 `&` 等 HTML 字符会按原样呈现而不会被转义。
3. 当你的 API 消费者期望原始的、未转义的 JSON 时使用 `PureJSON`。当响应可能嵌入 HTML 页面时使用标准的 `JSON`。
4. 示例
``` go
router.GET("/json", func(c *gin.Context) {
	c.JSON(http.StatusOK, gin.H{
		"html": "<b>Hello, world!</b>",
	}) 
	// Output: {"html":"\u003cb\u003eHello, world!\u003c/b\u003e"}
})

router.GET("/purejson", func(c *gin.Context) {
	c.PureJSON(http.StatusOK, gin.H{
		"html": "<b>Hello, world!</b>",
	}) 
	// Output: {"html":"<b>Hello, world!</b>"}
})
```