# C#语言入门学习笔记(一) #
## 前言 ##
&nbsp;&nbsp;C# 是专为公共语言基础结构（CLI）设计的。CLI 由可执行代码和运行时环境组成，允许在不同的计算机平台和体系结构上使用各种高级语言。虽然 C# 的构想十分接近于传统高级语言 C 和 C++，是一门面向对象的编程语言，但是它与 Java 非常相似，有许多强大的编程功能，因此得到广大程序员的青睐。
下面列出 C# 一些重要的功能：

-	布尔条件（Boolean Conditions）
-	自动垃圾回收（Automatic Garbage Collection）
-	标准库（Standard Library）
-	组件版本（Assembly Versioning）
-	属性（Properties）和事件（Events）
-	委托（Delegates）和事件管理（Events Management）
-	易于使用的泛型（Generics）
-	索引器（Indexers）
-	条件编译（Conditional Compilation）
-	简单的多线程（Multithreading）
-	LINQ 和 Lambda 表达式
-	集成 Windows

## C#数据类型 ##
### 值类型 ###
|类型|描述|默认值|
|:-|:-|:-|
|bool|布尔值|False|
|byte|8位无符号整数|0|
|char|16位Unicode字符|'\0'|
|decimal|128位精确的十进制值，28-29有效位数|0.0M|
|double|64位双精度浮点型|0.0D|
|float|32位单精度浮点型|0.0F|
|int|32位有符号整数类型|0|
|long|64位有符号整数类型|0L|
|sbyte|8位有符号整数类型|0|
|short|16位有符号整数类型|0|
|uint|32位无符号整数类型|0|
|ulong|64位无符号整数类型|0|
|ushort|16位无符号整数类型|0|
**如需得到一个类型或一个变量在特定平台上的准确尺寸，可以使用 、`sizeof` 方法。表达式 `sizeof`(type) 产生以字节为单位存储对象或类型的存储尺寸。**
### 引用类型 ###
&nbsp;&nbsp;**引用类型不包含存储在变量中的实际数据，但它们包含对变量的引用。换句话说，它们指的是一个内存位置。使用多个变量时，引用类型可以指向一个内存位置。如果内存位置的数据是由一个变量改变的，其他变量会自动反映这种值的变化。内置的 引用类型有：（对象）object、（动态）dynamic**[动态类型与对象类型相似，但是对象类型变量的类型检查是在编译时发生的，而动态类型变量的类型检查是在运行时发生的。] **和 （字符串）string** [C# string 字符串的前面可以加 @（称作"逐字字符串"）将转义字符（\）当作普通字符对待]。
### 指针类型 ###
&nbsp;&nbsp;指针类型变量存储另一种类型的内存地址。C# 中的指针与 C 或 C++ 中的指针有相同的功能。
## C# 类型转换 ##
类型转换从根本上说是类型铸造，或者说是把数据从一种类型转换为另一种类型。在 C# 中，类型铸造有两种形式：

-	隐式类型转换 - 这些转换是 C# 默认的以安全方式进行的转换, 不会导致数据丢失。例如，从小的整数类型转换为大的整数类型，从派生类转换为基类。
-	显式类型转换 - 显式类型转换，即强制类型转换。显式转换需要强制转换运算符，而且强制转换会造成数据丢失。

## C#常量 ##
常量是固定值，程序执行期间不会改变。常量可以是任何基本数据类型，比如整数常量、浮点常量、字符常量或者字符串常量，还有枚举常量。
## C#运算符&C#判断&C#封装 ##
与C、C++、Java类似
## C#封装 ##
封装 被定义为"把一个或多个项目封闭在一个物理的或者逻辑的包中"。在面向对象程序设计方法论中，封装是为了防止对实现细节的访问。抽象和封装是面向对象程序设计的相关特性。抽象允许相关信息可视化，封装则使开发者实现所需级别的抽象。C# 封装根据具体的需要，设置使用者的访问权限，并通过**访问修饰符**来实现。一个 访问修饰符 定义了一个类成员的范围和可见性。C# 支持的访问修饰符如下所示：

	public：所有对象都可以访问；
	private：对象本身在对象内部可以访问；
	protected：只有该类对象及其子类对象可以访问
	internal：同一个程序集的对象可以访问；
	protected internal：访问限于当前程序集或派生自包含类的类型。

## C#方法 ##
先定义再使用，依旧与Java相同。但是在Java中方法使用值传递和引用传递，这一点C#与Java略有不同。

|方式|描述|
|:-|:-|
|值参数|这种方式复制参数的实际值给函数的形式参数，实参和形参使用的是两个不同内存中的值。在这种情况下，当形参的值发生改变时，不会影响实参的值，从而保证了实参数据的安全。|
|引用参数(ref)|这种方式复制参数的内存位置的引用给形式参数。这意味着，当形参的值发生改变时，同时也改变实参的值。|
|输出参数(out)|这种方式可以返回多个值。|
## C# 可空类型（Nullable） ##
-	? : 单问号用于对 int,double,bool 等无法直接赋值为 null 的数据类型进行 null 的赋值，意思是这个数据类型是 NullAble 类型的。
-	??:用于定义可空类型和引用类型的默认值。Null 合并运算符为类型转换定义了一个预设值，以防可空类型的值为 Null。Null 合并运算符把操作数类型隐式转换为另一个可空（或不可空）的值类型的操作数的类型。
#### 至此 	*MutualExclusion 10/12/2019 9:40:48 AM* ####