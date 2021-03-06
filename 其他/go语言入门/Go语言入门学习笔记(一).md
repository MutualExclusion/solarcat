# Go语言入门学习笔记(一) #
## Go语言结构 ##
&nbsp;&nbsp;Go语言的基础组成有以下几个部分：

-	包声明 package
-	引入包 import
-	函数 func
-	变量 
-	语句 & 表达式
-	注释 /\*...\*/、//

&nbsp;&nbsp;说明：当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Solar，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 protected ）。
## Go语言基础语法 ##
### Go标记 ###
&nbsp;&nbsp;Go程序可以有多个标记组成，可以是关键字，标识符，常量，字符串，符号构成。

	fmt.Println("Hello, World!")

6 个标记是(每行一个)：

	fmt
	.
	Println
	(
	"Hello, World!"
	)

### 标识符 ###
&nbsp;&nbsp;标识符是由字母数字以及下划线组成序列，并且第一字符必须是字母或者是下划线儿不能是数字。
## Go语言数据类型 ##
### 布尔型 ###
&nbsp;&nbsp;常量 true 或者 false
### 数字类型 ###
#### 整形 ####
|序号|类型|描述|
|:-|:-:|:-|
|1|uint8| 无符号8位整型|
|2|uint16|无符号16位整型|
|3|uint32|无符号32位整型|
|4|uint64|无符号64位整型|
|5|int8|有符号8位整型|
|6|int16|有符号16位整型|
|7|int32|有符号32位整型|
|8|int64|有符号64位整型|
#### 浮点型 ####
|序号|类型|描述|
|:-|:-:|:-|
|1|float32|32位浮点型数|
|2|float64|64位浮点型数|
|3|complex64|32位实数和虚数|
|4|complex128|64位实数和虚数|
#### 其他数字类型 ####
|序号|类型|描述|
|:-|:-:|:-|
|1|byte|类似uint8|
|2|rune|类似int32|
|3|uint|32或64位|
|4|int|与uint一样大小|
|5|uintptr|无符号整型，用于存放一个指针|
### 字符串类型 ###
### 派生类型 ###