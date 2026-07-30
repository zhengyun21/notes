1. ![[Pasted image 20260729103636.png]]
2. 请求示例
	1. ``` Go
	func main() {  
		r := gin.Default()  
	    
	    r.GET("/ping", func(c *gin.Context) {  
	       c.JSON(http.StatusOK, gin.H{  
	          "message": "ok",  
	       })  
	    })  
	
	    r.HEAD("head", func(c *gin.Context) {  
	    c.Status(http.StatusOK)  
		})
	    
	    r.Run()  
	}
	   ```
	   	   