1. **基础查询方法**
``` go
var food1 Food  
db.First(&food1)     // order by primary key and asc limit 1  

var food2 Food  
db.Last(&food2)      // order by primary key and desc limit 1  

var foods []Food  
db.Find(&foods)      // select * from foods  

var randomFood Food  
db.Take(&randomFood) // random record
```
2. **条件查询**
``` go
var food1 Food
db.First(&food1, 4) // select by primary key  

var food2 Food  
db.Where("type_id = ?", 3).Find(&food2) // select by one colomn value  

var food3 Food  
db.Where("name = ? AND type_id = ?", "水果", 1).Find(&food3) // select by multi colomn values  

var food4 Food  
db.Where(&Food{  
    Name: "鸡腿",  
}).Find(&food4) // select by struct Food  

var food5 Food  
db.Where(map[string]interface{}{  
    "name": "水果",  
}).Find(&food5)  // select by mapper
/*  db.Where(gin.H{  
    "name": "水果",  
	}).Find(&food5) */ //无法用 gin.H，Go 里命名类型和它的底层类型在 type switch / 类型断言中不互通
```
3. **高级查询**
```go
var foods []Food
db.Where("type_id in ?", []int{1, 3}).Find(&foods) // In Query

var foods2 []Food
db.Where("type_id between ? and ?", 1, 3).Find(&foods2) // Between Query

var foods3 []Food
db.Where("name like ?", "%肉").Find(&foods3) // Like Query

var foods4 []Food
db.Order("type_id asc").Find(&foods4)  // Order Query

var foods5 []Food
db.Limit(5).Offset(5).Order("type_id desc").Find(&foods5) // SELECT * FROM foods ORDER BY type_id desc LIMIT 5 OFFSET 5

var count int64
db.Model(&Food{}).Count(&count)  // Count Query

var total int64
db.Model(&Food{}).Select("SUM(type_id)").Row().Scan(&total) // SUM Query

// Model(&Food{})：知道操作的表是 foods
// Select("SUM(type_id)")：查询的内容 select xxx
// Row()：要求结果恰好一行一列
// Scan(&total)：Row() 返回的是 *sql.Row, 所以 .Scan(&total) 调的是标准库那个, 把单行单列结果扫到 total
```
4. **关联查询**
``` go
// gorm.Model from source code `model.go`
type Model struct { 
	ID uint `gorm:"primarykey"` 
	CreatedAt time.Time 
	UpdatedAt time.Time 
	DeletedAt DeletedAt `gorm:"index"` 
}

type Article struct {  
    gorm.Model  
    Title  string  
    UserId uint  
}  
  
type User struct {  
    gorm.Model  
    Name     string  
    Articles []Article  
}

var user User  
db.Preload("Articles").First(&user, 1) // type User 里的字段名 "Articles"  

var user2 User  
db.Preload("Articles", "deleted_at IS null").First(&user2, 2)  // with parameter
db.Preload("Articles", "status = ?","published").First(&user2,2) // with parameter
```
5. **原生SQL查询**
``` go
// 原生查询 
db.Raw("SELECT * FROM users WHERE name = ?", "张三").Scan(&user) 

// 执行SQL 
db.Exec("DELETE FROM users WHERE age < ?", 18)
```
6. **查询链 链式调用**
``` go
db.Where("name LIKE ?", "%三%") 
	.Or("age > ?", 30) 
	.Order("created_at DESC") 
	.Limit(5) 
	.Find(&users)
```
7. **注意事项**
	1. **零值问题**：结构体条件查询会忽略零值字段（如`0`、`""`、`false`），改用`map`条件可避免
	2. **软删除**：使用`gorm.Model`的模型默认支持软删除，已删除记录不会被查询
	3. **错误处理**：查询结果需检查错误，如`db.First(&user).Error`
	4. **性能优化**：复杂查询建议使用原生SQL或手动构建查询