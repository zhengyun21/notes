1. **逻辑删除**
``` go
var user User
// 1. 先查询要删除的记录（根据 ID 查询）
db.First(&user, 1)
// 2. 执行逻辑删除
result := db.Delete(&user)
if result.Error != nil {
	log.Fatalf("删除失败: %v", result.Error)
	return
}
```
2. **直接删除**
``` go
var user2 User
db.Unscoped().First(&user2, 13)
result2 := db.Unscoped().Delete(&user2)
if result2.Error != nil {
    panic("硬删除失败")
}
```
3. **批量删除**
``` go
db.Where("id >= ?", 9).Delete(&User{}) // 逻辑删除
db.Unscoped().Where("email LIKE ?", "%test.com%").Delete(&User{}) // 直接删除
```
4. **恢复软删除记录**
``` go
var user User
// 1. 查询被软删除的记录（需使用 Unscoped 或指定 DeletedAt 条件）
db.Unscoped().First(&user, 1)
// 2. 恢复记录（清空 DeletedAt 字段）
result := db.Restore(&user)
if result.Error != nil {
	log.Fatalf("恢复失败: %v", result.Error)
	return
}
```
5. **注意事项**
	1. 常规查询（如 `Find`/`First`）会自动过滤掉 `DeletedAt` 非空的记录，如需查询软删除记录，需使用 `Unscoped`
	2. 硬删除会永久删除数据，执行前务必确认条件正确，建议先通过 `Where` 条件验证数据范围。
	3. 删除操作会触发 GORM 的钩子函数（如 `BeforeDelete`/`AfterDelete`），可用于自定义逻辑（如日志记录）。  复杂删除操作建议包裹在事务中