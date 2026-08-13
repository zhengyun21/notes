1. 示例代码
``` go
package main  
  
import (  
    "gorm.io/driver/mysql"  
    "gorm.io/gen"    
    "gorm.io/gorm"
)  
  
func main() {  
    // 1. 连接数据库（用你 Gdemo03 里相同的 DSN）  
    dsn := "admin:admin123@tcp(172.28.37.216:3306)/gorm?charset=utf8mb4&parseTime=True&loc=Local"  
    db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})  
    if err != nil {  
       panic(err)  
    }  
  
    // 2. 创建生成器实例  
    g := gen.NewGenerator(gen.Config{  
       OutPath:      "./query", // 生成的查询代码输出目录（相对当前 main.go）  
       ModelPkgPath: "./model", // 生成的 Model 输出目录  
       // Mode:      gen.WithoutContext, // 若不需要带 context 的方法可开启  
    })  
  
    // 3. 复用已有的 db 连接  
    g.UseDB(db)  
  
    // 4. 【方式一】生成数据库里所有表的 Model    g.ApplyBasic(g.GenerateAllTable()...)  
  
    // 【方式二】只生成指定表（推荐，更可控）  
    // g.ApplyBasic(  
    //     g.GenerateModel("users"),    //     g.GenerateModel("articles"),    // )  
    // 【方式三】为某张表自定义字段类型/映射  
    // user := g.GenerateModel("users",  
    //     gen.FieldType("age", "int"),           // 指定字段 Go 类型  
    //     gen.FieldComment("name", "用户名"),      // 字段注释  
    //     gen.FieldGORMTag("id", "primaryKey"),  // 自定义 GORM tag    // )    // g.ApplyBasic(user)  
    // 5. 执行生成  
    g.Execute()  
}
```
2. 生成的文件目录：
![[Pasted image 20260813170712.png|276]]
`model` 里的文件定义了数据库表对应的go结构体
`query` 里的文件是一些查询方法，例如：
``` go
func (u userDo) Where(conds ...gen.Condition) *userDo { ... } 
func (u userDo) Order(conds ...field.Expr) *userDo { ... } 
func (u userDo) Limit(limit int) *userDo { ... } 
func (u userDo) Create(values ...*model.User) error { ... } 
func (u userDo) First() (*model.User, error) { ... } 
func (u userDo) Find() ([]*model.User, error) { ... } 
func (u userDo) Delete(models ...*model.User) (gen.ResultInfo, error) { ... }
```
3. 如何调用上面这些方法
``` go
// 用 `query.Use(db)` 把 `*gorm.DB` 包装成 `*Query`，然后用 `*Query` 操作。
// 1. 第一步：用 gorm.Open 拿到 *gorm.DB（和原来一样） 
dsn := "admin:admin123@tcp(172.28.37.216:3306)/gorm?charset=utf8mb4&parseTime=True&loc=Local" 

db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{}) 

if err != nil { 
	panic(err) 
} 

// 2. 第二步：用 query.Use(db) 包装成 *Query 
Q = query.Use(db) 

// 3. 之后用 Q 操作，不再直接用 db 
testQuery()
```