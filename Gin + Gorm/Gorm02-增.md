``` go
type Food struct {  
    Id        int  
    Name      string  
    Price     float64  
    TypeId    int  
    CreatTime int  
}

food := Food{       
	Id:     6,       
	Name:   "鸡腿",  
	Price:  12,       
	TypeId: 003,    
}    

result := db.Omit("CreateTime").Create(&food) // Omit 忽略 CreateTime这个字段的插入，映射到表会自动转为 create_time

result := db.Select("CreateTime").Create(&food) // Select 只选择 CreateTime这个字段进行插入，映射到表会自动转为 create_time，其他字段不插入，数据库里的值为空

fmt.Println(result.RowsAffected) //返回成功条数
```