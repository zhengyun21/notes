1. **更新单个记录**
``` go
var user1 User  
db.First(&user1, 2)  
user1.Name = "zzy"  
db.Save(&user1)
```
![[Pasted image 20260813145404.png]]
2. **指定字段更新**
``` go
var user2 User  
db.First(&user2, 1) 
db.Model(&user2).Select("name").Updates(User{Name: "zzx"}) // 对 name 进行更新
db.Model(&user2).Omit("Email").Updates(User{Name: "新名字"}) // 忽略对 email 进行更新
```
![[Pasted image 20260813145343.png]]
3. **条件更新**
``` go
db.Model(&User{}).Where("age > ?", 30).Update("status", "VIP")

// 从 Updates() 传入的参数类型推断表名
db.Where("id = ?", 3).Select("name").Updates(User{Name: "zzx"})

// 这种就不会推断表名了，必须 Table() 指定，同时可以修改多个字段
db.Table("users").Where("id = ?", 4).Updates(map[string]interface{}{"name":"zzt"})
```
4. **高级更新技巧**
``` go
// 原子性地增加某个字段的值（避免并发问题）
db.Model(&User{}).Where("id = ?", 1).Update("age", gorm.Expr("age + ?", 1))

// 默认情况下，零值（如 0、""、false）不会被更新
// 若需要更新零值，使用 map 方式：
db.Model(&user).Updates(map[string]interface{}{ 
	"age": 0, // 会更新为 0
	"name": "", // 会更新为空字符串
	"is_vip": false, // 会更新为 false
})
```