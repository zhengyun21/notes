``` go
import (  
    "gorm.io/driver/mysql"    
    "gorm.io/gorm"
)

func main() {  
    db, err := gorm.Open(mysql.New(mysql.Config{  
       DSN: "admin:admin123@tcp(172.28.37.216:3306)/gorm?charset=utf8mb4&parseTime=True&loc=Local",
       DefaultStringSize:         256,
       DisableDatetimePrecision:  true,
       DontSupportRenameIndex:    true,
       DontSupportRenameColumn:   true,
       SkipInitializeWithVersion: false,  
    }), &gorm.Config{})  
    
    if err != nil {  
       panic("连接失败error: " + err.Error())  
       return  
    }
}
```