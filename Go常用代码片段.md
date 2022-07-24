---
title: Go常用代码片段
date: 2022-07-22 22:50:23
categories: Go
tags: 代码片段
---
# Go常用代码段

## 循环

```go
//无限循环
for {
    // pass
}
for true {
    // pass
}
//条件循环
sum := 100
for sum < 10 {
    // pass
}
//固定数量循环
for i:=0; i <= 10 ; i ++ {
    // pass
}
```



## 类型

### 基本

Go允许在底层结构相同的两个类型之间互转

```go
// IT类型的底层是int类型
type IT int
// a的类型为IT，底层是int
var a IT = 5
// 将a(IT)转换为int，b现在是int类型
b := int(5)
// 将b(int)转换为IT，c现在是IT类型
c := IT(b)
```

### strconv

strconv包提供了跨类型之间的转换

#### string和int的转换

```go
// Itoa(): int -> string
println("a" + strconv.Itoa(32))  // a32

// Atoi(): string -> int
i,_ := strconv.Atoi("3")
println(3 + i)   // 6

// Atoi()转换失败
i,err := strconv.Atoi("a")
if err != nil {
    println("converted failed")
}
```

#### Parse函数

将string类型包装成各种类型

```go
b, err := strconv.ParseBool("true")
f, err := strconv.ParseFloat("3.1415", 64)
i, err := strconv.ParseInt("-42", 10, 64)
u, err := strconv.ParseUint("42", 10, 64)
```

#### Format函数

将各种类型格式化为string类型

```go
s := strconv.FormatBool(true)
s := strconv.FormatFloat(3.1415, 'E', -1, 64)
s := strconv.FormatInt(-42, 16)
s := strconv.FormatUint(42, 16)
```

## 字符

```go
//判断是否为字母
unicode.IsLetter(ch)
//判断是否为数字
unicode.IsDigit(ch)
//判断是否为空白符号
unicode.IsSpace(ch)
```

## 字符串

### 初始化

```go
s1 := "hello world"

// 创建多行字符串
s2 := `This is a
multiline
string.`
```

### 遍历

```go
type byte = uint8
type rune = int32
str := "Hello，世界"

//下标遍历
//这种遍历方式是以byte的形式进行遍历, 多字节字符会出现乱码
for i := 0; i < len(str); i++ {
    c := str[i]
    fmt.Printf("%c 的类型是 %s\n", c, reflect.TypeOf(c))
}
/* Output:
H 的类型是 uint8（ASCII 字符返回正常）
e 的类型是 uint8
l 的类型是 uint8
l 的类型是 uint8
o 的类型是 uint8
ï 的类型是 uint8（从这里开始出现了奇怪的乱码）
¼ 的类型是 uint8
 的类型是 uint8
ä 的类型是 uint8
¸ 的类型是 uint8
 的类型是 uint8
ç 的类型是 uint8
 的类型是 uint8
 的类型是 uint8
*/

//range遍历
//这种遍历方式会议rune类型进行遍历, 没有乱码问题
for _, c := range str {
    fmt.Printf("%c 的类型是 %s\n", c, reflect.TypeOf(c))
}
/* Output:
H 的类型是 int32
e 的类型是 int32
l 的类型是 int32
l 的类型是 int32
o 的类型是 int32
， 的类型是 int32
世 的类型是 int32
界 的类型是 int32
*/

//也可以转换为rune数组进行遍历
strRunes := []rune(str)
for i := 0; i < len(strRunes); i++ {
    c := strRunes[i]
    fmt.Printf("%c 的类型是 %s\n", c, reflect.TypeOf(c))
}
//输出和上面一样
```

### 修改

```go
// 字符串的值是不可变的，可以分配一个新字符串值
s := "hello"
t := s

// 将字符串转为[]byte或[]rune可以进行修改
s1 := "hello world"
s2 := []byte(s1)
s2[0] = 'H'
s3 := string(s2)
```

### 包含

```go
 // 判断字符串s的i索引位置字符是否是元音
 if strings.Contains("aeiouAEIOU", string(s[i])) {
 	// ...
 }
```

### 拼接

```go
// 支持直接用+进行连接，但是效率不高
s1 := "hello "
s2 := s1 + "world"

// bytes.Buffer可以一次性连接
var b bytes.Buffer
b.WriteString("Hello ")
b.WriteString("World")
b1 := b.String()

// 多个字符串拼接
var strs []string
strings.Join(strs, "World")
```

### 分割

```go
str := "赵,钱,孙,李,赵"

//字符串分割, 使用字符分割
str1 := strings.Split(str, ",")
fmt.Println(str1[0]) //赵
fmt.Println(str1[1]) //钱
fmt.Println(str1[2]) //孙
fmt.Println(str1[3]) //李
fmt.Println(str1[4]) //赵

//字符串替换, -1表示全部替换, 0表示不替换, 1表示替换第一个, 2表示替换第二个...
str2 := strings.Replace(str, "赵", "钱", -1)
fmt.Println(str2) //钱,钱,孙,李,钱

//字符串截取, 一个汉字3个字节, 还有一个逗号
str3 := str[4 : len(str)-4]
fmt.Println(str3) //钱,孙,李
```

## 排序

### 升序

```go
intList := [] int {2, 4, 3, 5, 7, 6, 9, 8, 1, 0}
float8List := [] float64 {4.2, 5.9, 12.3, 10.0, 50.4, 99.9, 31.4, 27.81828, 3.14}
stringList := [] string {"a", "c", "b", "d", "f", "i", "z", "x", "w", "y"}
   
sort.Ints(intList)
sort.Float64s(float8List)
sort.Strings(stringList)
   
fmt.Printf("%v\n%v\n%v\n", intList, float8List, stringList)
/* Output:
[0 1 2 3 4 5 6 7 8 9]
[3.14 4.2 5.9 10 12.3 27.81828 31.4 50.4 99.9]
[a b c d f i w x y z]
*/
```

### 降序

```go
intList := [] int {2, 4, 3, 5, 7, 6, 9, 8, 1, 0}
float8List := [] float64 {4.2, 5.9, 12.3, 10.0, 50.4, 99.9, 31.4, 27.81828, 3.14}
stringList := [] string {"a", "c", "b", "d", "f", "i", "z", "x", "w", "y"}
   
sort.Sort(sort.Reverse(sort.IntSlice(intList)))
sort.Sort(sort.Reverse(sort.Float64Slice(float8List)))
sort.Sort(sort.Reverse(sort.StringSlice(stringList)))
   
fmt.Printf("%v\n%v\n%v\n", intList, float8List, stringList)
/* Output:
[9 8 7 6 5 4 3 2 1 0]
[99.9 50.4 31.4 27.81828 12.3 10 5.9 4.2 3.14]
[z y x w i f d c b a]
*/
```

### 非基本类型排序

```go
type Person struct {
    Name string
    Age  int
}
// 按照 Person.Age 从大到小排序
type PersonSlice [] Person
func (a PersonSlice) Len() int {    	 // 重写 Len() 方法
    return len(a)
}
func (a PersonSlice) Swap(i, j int){     // 重写 Swap() 方法
    a[i], a[j] = a[j], a[i]
}
func (a PersonSlice) Less(i, j int) bool {    // 重写 Less() 方法， 从大到小排序
    return a[i].Age < a[j].Age
}
 
func main() {
    people := [] Person{
        {"zhang san", 12},
        {"li si", 30},
        {"wang wu", 52},
        {"zhao liu", 26},
    }
    sort.Sort(PersonSlice(people))    // 按照 Age 的逆序排序
    fmt.Println(people)
 
    sort.Sort(sort.Reverse(PersonSlice(people)))    // 按照 Age 的升序排序
    fmt.Println(people)
}
/* Output:
[{zhang san 12} {zhao liu 26} {li si 30} {wang wu 52}]
[{wang wu 52} {li si 30} {zhao liu 26} {zhang san 12}]
*/
```

## 查找

内建了二分法查找

```go
ints := []int{}
floats := []float64{}
strings := []string{}
sort.SearchInts(ints, 1)
sort.SearchFloat64s(floats, 1.0)
sort.SearchStrings(strings, "1")
```

## 进制

```go
//十进制 -> 二进制
a := strconv.FormatInt(255, 2)
//二进制 -> 十进制
b, _ := strconv.ParseInt("0100", 2, 32)
//十进制 -> 二进制(前面不够的位数补0)
c := fmt.Sprintf("%08b", b)

fmt.Println(a) //11111111
fmt.Println(b) //4
fmt.Println(c) //00000100

//统计二进制中1的个数
fmt.Println(bits.OnesCount(uint(b))) //1
```

## 数据结构

### 数组

```go
// 初始化一个大小为10，默认值为0的数组
nums := make([10]int)
// 初始化一个二位boolean数组
visited := make([5][10]int)

for i := 0; i < len(nums); i++ {
    // 访问num[i]
}
```

### 切片

切片本身可以视作链表使用

```go
// 初始化一个存储String类型的切片
slice := make([]string, 0)
slice := []string{}

// 初始化一个存储int类型的切片
slice := make([]int, 0)
slice := []int{}
```

### 实现栈

```go
// 创建栈
stack := make([]int, 0)
// push压入
stack = append(stack, 10)
// pop弹出
v := stack[len(stack) - 1]
stack = stack[:len(stack) - 1]
// 检查栈空
len(stack) == 0
```

### 实现队列

```go
// 创建队列
queue := make([]int, 0)
// enqueue入队
queue = append(queue, 10)
// dequeue出队
v := queue[0]
queue = queue[1:]
// 长度0为空
len(queue) == 0
```

### Map

```go
// 创建
m := make(map[string]int)
// 设置kv
m["hello"] = 1
// 删除k
delete(m,"hello")
// 遍历
for k, v := range m{
    // 操作
}

// map键需要可比较，不能为slice、map、function
// map值都有默认值，可以直接操作默认值，如：m[age]++ 值由0变为1
// 比较两个map需要遍历，其中的kv是否相同，因为有默认值关系，所以需要检查val和ok两个值
```

## 附录

### 基本类型

| 类型          | 长度(字节) | 默认值 | 说明                                      |
| ------------- | ---------- | ------ | ----------------------------------------- |
| bool          | 1          | false  |                                           |
| byte          | 1          | 0      | uint8                                     |
| rune          | 4          | 0      | Unicode Code Point, int32                 |
| int, uint     | 4或8       | 0      | 32 或 64 位                               |
| int8, uint8   | 1          | 0      | -128 ~ 127, 0 ~ 255，byte是uint8 的别名   |
| int16, uint16 | 2          | 0      | -32768 ~ 32767, 0 ~ 65535                 |
| int32, uint32 | 4          | 0      | -21亿~ 21亿, 0 ~ 42亿，rune是int32 的别名 |
| int64, uint64 | 8          | 0      |                                           |
| float32       | 4          | 0.0    |                                           |
| float64       | 8          | 0.0    |                                           |
| complex64     | 8          |        |                                           |
| complex128    | 16         |        |                                           |
| uintptr       | 4或8       |        | 以存储指针的 uint32 或 uint64 整数        |
| array         |            |        | 值类型                                    |
| struct        |            |        | 值类型                                    |
| string        |            | ""     | UTF-8 字符串                              |
| slice         |            | nil    | 引用类型                                  |
| map           |            | nil    | 引用类型                                  |
| channel       |            | nil    | 引用类型                                  |
| interface     |            | nil    | 接口                                      |
| function      |            | nil    | 函数                                      |

### Range表

| 类型        | 1st value | 2nd value |
| ----------- | --------- | --------- |
| string      | index     | s[index]  |
| array/slice | index     | s[index]  |
| map         | key       | m[key]    |
| channel     | element   |           |

### ASCII码表

| 二进制   | 十进制 | 十六进制 | 字符/缩写                                    | 解释                               |
| -------- | ------ | -------- | -------------------------------------------- | ---------------------------------- |
| 00000000 | 0      | 00       | NUL (NULL)                                   | 空字符                             |
| 00000001 | 1      | 01       | SOH (Start Of Headling)                      | 标题开始                           |
| 00000010 | 2      | 02       | STX (Start Of Text)                          | 正文开始                           |
| 00000011 | 3      | 03       | ETX (End Of Text)                            | 正文结束                           |
| 00000100 | 4      | 04       | EOT (End Of Transmission)                    | 传输结束                           |
| 00000101 | 5      | 05       | ENQ (Enquiry)                                | 请求                               |
| 00000110 | 6      | 06       | ACK (Acknowledge)                            | 回应/响应/收到通知                 |
| 00000111 | 7      | 07       | BEL (Bell)                                   | 响铃                               |
| 00001000 | 8      | 08       | BS (Backspace)                               | 退格                               |
| 00001001 | 9      | 09       | HT (Horizontal Tab)                          | 水平制表符                         |
| 00001010 | 10     | 0A       | LF/NL(Line Feed/New Line)                    | 换行键                             |
| 00001011 | 11     | 0B       | VT (Vertical Tab)                            | 垂直制表符                         |
| 00001100 | 12     | 0C       | FF/NP (Form Feed/New Page)                   | 换页键                             |
| 00001101 | 13     | 0D       | CR (Carriage Return)                         | 回车键                             |
| 00001110 | 14     | 0E       | SO (Shift Out)                               | 不用切换                           |
| 00001111 | 15     | 0F       | SI (Shift In)                                | 启用切换                           |
| 00010000 | 16     | 10       | DLE (Data Link Escape)                       | 数据链路转义                       |
| 00010001 | 17     | 11       | DC1/XON (Device Control 1/Transmission On)   | 设备控制1/传输开始                 |
| 00010010 | 18     | 12       | DC2 (Device Control 2)                       | 设备控制2                          |
| 00010011 | 19     | 13       | DC3/XOFF (Device Control 3/Transmission Off) | 设备控制3/传输中断                 |
| 00010100 | 20     | 14       | DC4 (Device Control 4)                       | 设备控制4                          |
| 00010101 | 21     | 15       | NAK (Negative Acknowledge)                   | 无响应/非正常响应/拒绝接收         |
| 00010110 | 22     | 16       | SYN (Synchronous Idle)                       | 同步空闲                           |
| 00010111 | 23     | 17       | ETB (End of Transmission Block)              | 传输块结束/块传输终止              |
| 00011000 | 24     | 18       | CAN (Cancel)                                 | 取消                               |
| 00011001 | 25     | 19       | EM (End of Medium)                           | 已到介质末端/介质存储已满/介质中断 |
| 00011010 | 26     | 1A       | SUB (Substitute)                             | 替补/替换                          |
| 00011011 | 27     | 1B       | ESC (Escape)                                 | 逃离/取消                          |
| 00011100 | 28     | 1C       | FS (File Separator)                          | 文件分割符                         |
| 00011101 | 29     | 1D       | GS (Group Separator)                         | 组分隔符/分组符                    |
| 00011110 | 30     | 1E       | RS (Record Separator)                        | 记录分离符                         |
| 00011111 | 31     | 1F       | US (Unit Separator)                          | 单元分隔符                         |
| 00100000 | 32     | 20       | (Space)                                      | 空格                               |
| 00100001 | 33     | 21       | !                                            |                                    |
| 00100010 | 34     | 22       | "                                            |                                    |
| 00100011 | 35     | 23       | #                                            |                                    |
| 00100100 | 36     | 24       | $                                            |                                    |
| 00100101 | 37     | 25       | %                                            |                                    |
| 00100110 | 38     | 26       | &                                            |                                    |
| 00100111 | 39     | 27       | '                                            |                                    |
| 00101000 | 40     | 28       | (                                            |                                    |
| 00101001 | 41     | 29       | )                                            |                                    |
| 00101010 | 42     | 2A       | *                                            |                                    |
| 00101011 | 43     | 2B       | +                                            |                                    |
| 00101100 | 44     | 2C       | ,                                            |                                    |
| 00101101 | 45     | 2D       | -                                            |                                    |
| 00101110 | 46     | 2E       | .                                            |                                    |
| 00101111 | 47     | 2F       | /                                            |                                    |
| 00110000 | 48     | 30       | 0                                            |                                    |
| 00110001 | 49     | 31       | 1                                            |                                    |
| 00110010 | 50     | 32       | 2                                            |                                    |
| 00110011 | 51     | 33       | 3                                            |                                    |
| 00110100 | 52     | 34       | 4                                            |                                    |
| 00110101 | 53     | 35       | 5                                            |                                    |
| 00110110 | 54     | 36       | 6                                            |                                    |
| 00110111 | 55     | 37       | 7                                            |                                    |
| 00111000 | 56     | 38       | 8                                            |                                    |
| 00111001 | 57     | 39       | 9                                            |                                    |
| 00111010 | 58     | 3A       | :                                            |                                    |
| 00111011 | 59     | 3B       | ;                                            |                                    |
| 00111100 | 60     | 3C       | <                                            |                                    |
| 00111101 | 61     | 3D       | =                                            |                                    |
| 00111110 | 62     | 3E       | >                                            |                                    |
| 00111111 | 63     | 3F       | ?                                            |                                    |
| 01000000 | 64     | 40       | @                                            |                                    |
| 01000001 | 65     | 41       | A                                            |                                    |
| 01000010 | 66     | 42       | B                                            |                                    |
| 01000011 | 67     | 43       | C                                            |                                    |
| 01000100 | 68     | 44       | D                                            |                                    |
| 01000101 | 69     | 45       | E                                            |                                    |
| 01000110 | 70     | 46       | F                                            |                                    |
| 01000111 | 71     | 47       | G                                            |                                    |
| 01001000 | 72     | 48       | H                                            |                                    |
| 01001001 | 73     | 49       | I                                            |                                    |
| 01001010 | 74     | 4A       | J                                            |                                    |
| 01001011 | 75     | 4B       | K                                            |                                    |
| 01001100 | 76     | 4C       | L                                            |                                    |
| 01001101 | 77     | 4D       | M                                            |                                    |
| 01001110 | 78     | 4E       | N                                            |                                    |
| 01001111 | 79     | 4F       | O                                            |                                    |
| 01010000 | 80     | 50       | P                                            |                                    |
| 01010001 | 81     | 51       | Q                                            |                                    |
| 01010010 | 82     | 52       | R                                            |                                    |
| 01010011 | 83     | 53       | S                                            |                                    |
| 01010100 | 84     | 54       | T                                            |                                    |
| 01010101 | 85     | 55       | U                                            |                                    |
| 01010110 | 86     | 56       | V                                            |                                    |
| 01010111 | 87     | 57       | W                                            |                                    |
| 01011000 | 88     | 58       | X                                            |                                    |
| 01011001 | 89     | 59       | Y                                            |                                    |
| 01011010 | 90     | 5A       | Z                                            |                                    |
| 01011011 | 91     | 5B       | [                                            |                                    |
| 01011100 | 92     | 5C       | \                                            |                                    |
| 01011101 | 93     | 5D       | ]                                            |                                    |
| 01011110 | 94     | 5E       | ^                                            |                                    |
| 01011111 | 95     | 5F       | _                                            |                                    |
| 01100000 | 96     | 60       | `                                            |                                    |
| 01100001 | 97     | 61       | a                                            |                                    |
| 01100010 | 98     | 62       | b                                            |                                    |
| 01100011 | 99     | 63       | c                                            |                                    |
| 01100100 | 100    | 64       | d                                            |                                    |
| 01100101 | 101    | 65       | e                                            |                                    |
| 01100110 | 102    | 66       | f                                            |                                    |
| 01100111 | 103    | 67       | g                                            |                                    |
| 01101000 | 104    | 68       | h                                            |                                    |
| 01101001 | 105    | 69       | i                                            |                                    |
| 01101010 | 106    | 6A       | j                                            |                                    |
| 01101011 | 107    | 6B       | k                                            |                                    |
| 01101100 | 108    | 6C       | l                                            |                                    |
| 01101101 | 109    | 6D       | m                                            |                                    |
| 01101110 | 110    | 6E       | n                                            |                                    |
| 01101111 | 111    | 6F       | o                                            |                                    |
| 01110000 | 112    | 70       | p                                            |                                    |
| 01110001 | 113    | 71       | q                                            |                                    |
| 01110010 | 114    | 72       | r                                            |                                    |
| 01110011 | 115    | 73       | s                                            |                                    |
| 01110100 | 116    | 74       | t                                            |                                    |
| 01110101 | 117    | 75       | u                                            |                                    |
| 01110110 | 118    | 76       | v                                            |                                    |
| 01110111 | 119    | 77       | w                                            |                                    |
| 01111000 | 120    | 78       | x                                            |                                    |
| 01111001 | 121    | 79       | y                                            |                                    |
| 01111010 | 122    | 7A       | z                                            |                                    |
| 01111011 | 123    | 7B       | {                                            |                                    |
| 01111100 | 124    | 7C       | \|                                           |                                    |
| 01111101 | 125    | 7D       | }                                            |                                    |
| 01111110 | 126    | 7E       | ~                                            |                                    |
| 01111111 | 127    | 7F       | DEL (Delete)                                 | 删除                               |
