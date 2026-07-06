1. 数组
	1. 规则
		1. 数组里的元素必须全部为同一类型，要嘛全部是字符串，要嘛全部是整数
		2. 声明数组时，必须指定其长度或者大小
	2. 声明
		1. var array [3]int = [3]int{1, 2, 3} fmt.Println(array) var array1 = [3]int{1, 2, 3} fmt.Println(array1) var array2 = [...]int{1, 2, 3} fmt.Println(array2)
	3. 修改
		1. var array1 = [3]int{1, 2, 3} array1[0] = 10 // 根据索引找到对应的元素位置，然后替换
2. 索引
	1. // 以定义一个字符串数组 a b c d 为例 var sList = []string{"a", "b", "c", "d"} // 正向索引就从0开始，从左往右 /* a b c d 0 1 2 3 */ // 取值就通过索引去取 fmt.Println(sList[0]) // 拿到a这个元素 // 当然，有的语言支持负向索引，go不支持 /* a b c d -4 -3 -2 -1 */ // 如果在go语言里面，我想拿到倒数第二个元素，怎么办？ fmt.Println(sList[len(sList)-2]) // 拿到a这个元素
3. 切片slice
	1. 很明显啊，go里面的数组，长度被限制死了，所以不经常用，所以go出了一个数组plus，叫做slice（切片），切片（Slice）相较于数组更灵活，因为在声明切片后其长度是可变的
	2. func main() { // 定义一个字符串切片 var list []string list = append(list, "枫枫") list = append(list, "知道") fmt.Println(list) fmt.Println(len(list)) // 切片长度 // 修改第二个元素 list[1] = "不知道" fmt.Println(list) }
4. make函数
	1. 除了基本数据类型，其他数据类型如果只定义不赋值，那么实际的值就是nil
	2. 