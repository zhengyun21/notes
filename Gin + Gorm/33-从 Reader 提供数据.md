1. `DataFromReader` 允许你将任何 `io.Reader` 的数据直接流式传输到 HTTP 响应，而无需先将整个内容缓冲到内存中。这对于构建代理端点或高效地从远程源提供大文件至关重要。
2. 方法签名为 `c.DataFromReader(code, contentLength, contentType, reader, extraHeaders)`。你需要提供 HTTP 状态码、内容长度、MIME 类型、要流式传输的 `io.Reader`，以及可选的额外响应头映射（如用于文件下载的 `Content-Disposition`）。
3. 示例
``` go
r.GET("/someDataFromReader", func(c *gin.Context) {  
    response, err := http.Get("https://raw.githubusercontent.com/gin-gonic/logo/master/color.png")  
  
    if response.StatusCode != http.StatusOK || err != nil {  
       c.Status(http.StatusServiceUnavailable)  
       return  
    }  
  
    reader := response.Body  
    length := response.ContentLength  
    contentType := response.Header.Get("content-type")  
    extraHeaders := map[string]string{  
       "Content-Disposition": `attachment; filename="gopher.png"`,  
    }  
    c.DataFromReader(200, length, contentType, reader, extraHeaders)  
})
```