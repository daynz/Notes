# C#

[toc]

## 简介

C# 是微软开发的在 .Net Framework 上运行的一种面向对象的编程语言。

C# 语言需要在公共语言运行库（CLR）上运行。

.Net Framework 是一个平台，它提供了一个跨语言的统一编程环境。而 C# 是一种在 .Net Framework 平台上使用的编程语言。

Mono 是 .NET Framework 的开源版本，其中包含了 C# 编译器，并且可以在多种操作系统（包括 Linux、Mac OS、Android、BSD、iOS、Linux、OS X、Windows、Solaris 和 UNIX）上运行，是开发跨平台 C# 应用程序的首选。

## Hello World！

```c#
using System;

namespace c.biancheng.net
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

## 数据类型

### 值类型

|  类型   |                    描述                     |                             范围                             | 默认值 |
| :-----: | :-----------------------------------------: | :----------------------------------------------------------: | :----: |
|  bool   |                   布尔值                    |                        True 或 False                         | False  |
|  byte   |               8 位无符号整数                |                           0 到 255                           |   0    |
|  char   |             16 位 Unicode 字符              |                      U +0000 到 U +ffff                      |  `\0`  |
| decimal | 128 位精确的十进制值，具有 28~29 个有效位数 | (-7.9 x 10<sup>28</sup> 到 7.9 x 10<sup>28</sup>) / 10<sup>0 到 28</sup> |  0.0M  |
| double  |              64 位双精度浮点型              | (+/-)5.0 x 10<sup>-324</sup> 到 (+/-) 1.7 x 10<sup>308</sup> |  0.0D  |
|  float  |              32 位单精度浮点型              |      -3.4 x 10<sup>38</sup> 到 + 3.4 x 10<sup>38</sup>       |  0.0F  |
|   int   |             32 位有符号整数类型             |               -2,147,483,648 到 2,147,483,647                |   0    |
|  long   |             64 位有符号整数类型             |   -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807    |   0L   |
|  sbyte  |             8 位有符号整数类型              |                         -128 到 127                          |   0    |
|  short  |             16 位有符号整数类型             |                      -32,768 到 32,767                       |   0    |
|  uint   |             32 位无符号整数类型             |                      0 到 4,294,967,295                      |   0    |
|  ulong  |             64 位无符号整数类型             |               0 到 18,446,744,073,709,551,615                |   0    |
| ushort  |             16 位无符号整数类型             |                         0 到 65,535                          |   0    |

获取类型或变量的确切大小使用sizeof

### 引用类型

C# 中内置的引用类型包括 Object（对象）、Dynamic（动态）和 string（字符串）。

####  对象类型（Object）

对象类型是 C# 通用类型系统（Common Type System：CTS）中所有数据类型的最终基类，Object 是 System.Object 类的别名。任何类型的值都可以分配给对象类型，但是在分配值之前，需要对类型进行转换。

#### 动态类型（Dynamic）

您可以在动态类型的变量中存储任何类型的值，这些变量的类型检查是在程序运行时进行的。动态类型的声明语法如下所示：

```c#
dynamic <variable_name> = value;
```

例如：

```C#
dynamic d = 20;
```

动态类型与对象类型类似，但对象类型变量的类型检查是在编译时进行的，而动态类型变量的类型检查则是在程序运行时进行的。

#### 字符串类型（String）

字符串类型的变量允许您将一个字符串赋值给这个变量，字符串类型需要通过 String 类来创建，String 类是 System.String 类的别名，它是从对象（Object）类型中派生的。在 C# 中有两种定义字符串类型的方式，分别是使用`" "`和`@" "`。

示例代码如下：

```c#
//使用引号的声明方式
String str = "http://c.biancheng.net/";
//使用 @ 加引号的声明形式
@"http://c.biancheng.net/";
```

使用`@" "`形式声明的字符串称为“逐字字符串”，逐字字符串会将转义字符`\`当作普通字符对待，例如`string str = @"C:\Windows";`等价于`string str = "C:\\Windows";`。

另外，在`@" "`形式声明的字符串中可以任意使用换行，换行符及缩进空格等都会计算在字符串的长度之中。

#### 指针类型

C# 语言中的指针是一个变量，也称为定位器或指示符，其中可以存储另一种类型的内存地址。C# 中的指针与  C 或 C++ 中的指针具有相同的功能。

## 变量

变量可以理解为是我们程序可以操作的内存区域的名称，在 C# 中每个变量都有自己特定的类型，这个类型确定了变量所占内存的大小、布局、取值范围以及可以对该变量执行的操作。

使用 `ReadLine()` 函数可以接受来自用户输入的内容并将其存储到变量中。

### 数据类型转换

数据类型转换就是将一种类型的数据转换为另一种类型

#### 隐式类型转换

隐式类型转换是由 C# 以类型安全的方式执行的，转换的过程中不会导致数据丢失。

#### 显式类型转换

显式类型转换也叫强制类型转换，这种转换需要使用`(type)value`的形式或者预定义函数显式的完成，显式转换需要用户明确的指定要转换的类型，而且在转换的过程中可能会造成数据丢失，例如将 double 类型转换为 int 类型。

## 运算符

### 特殊运算符

|  运算符  |                  描述                  |                             实例                             |
| :------: | :------------------------------------: | :----------------------------------------------------------: |
| sizeof() |           返回数据类型的大小           |                    sizeof(int)，将返回 4                     |
| typeof() |           返回 class 的类型            |                    typeof(StreamReader);                     |
|    &     |             返回变量的地址             |                   &a 将得到变量的实际地址                    |
|    *     |               变量的指针               |                     *a; 将指向一个变量。                     |
|   ? :    |           三元（三目）运算符           |       a>b ? X : Y; 如果条件为真，则值为 X : 否则值为 Y       |
|    is    |         判断对象是否为某一类型         |    if( Ford is Car) // 检查 Ford 是否是 Car 类的一个对象     |
|    as    | 强制转换，即使转换失败也不会抛出异常。 | Object obj = new StringReader("Hello"); StringReader r = obj as StringReader; |

## 参数传递

### 值传递

```C#
class Demo
{
    static void Main(string[] args){
        int val = 10;
        Demo.Func(val);
    }
    public static void Func(int val){
        val *= val;
        Console.WriteLine("函数内部的值：{0}", val);
    }
}
```

### 引用传递

参数必须要有初始值。

```C#
class Demo
{
    static void Main(string[] args){
        int val = 10;
        Demo.Func(ref val);
        Console.WriteLine("调用函数之后：{0}", val);
    }
    public static void Func(ref int val){
        val *= val;
        Console.WriteLine("函数内部的值：{0}", val);
    }
}
```

### 输出传递

控制离开当前方法之前必须对 out 参数赋值。

不能直接使用传入的参数。

```C#
class Demo
{
    static void Main(string[] args){
        int val = 33;
        Demo.getValue(out val);
        Console.WriteLine("调用函数之后 val 的值：{0}", val);
    }
    public static void getValue(out int x){
        int temp = 11;
        x = temp;
        x *= x;
    }
}
```

## 可空类型

### 声明

```c#
int? a;
```

a中存储 -2147483648 到 2147483647 之间的任何值或者 null。

### Null合并运算符

表达式`a??b`中，如果 a 不为空，那么表达式的值则为 a，反之则为 b。

Null 合并运算符左右两边操作数的类型必须相同，或者右操作数的类型可以隐式的转换为左操作数的类型，否则将编译错误。

## 数组

### 多维数组

#### 声明

```c#
int[,] arr = new int[3,3];// 声明一个二维数组
int[,,] arr=new int[3,3,3];// 声明一个三维数组 
```

### 交错数组

#### 声明

```C#
int[][] jaggedArray = new int[3][];
int[][,] jaggedArray = new int[3][,];
```

### 参数数组

```c#
public void Arr(params int[] arr) {}
```

### Array类

Array 类是 C# 中所有数组的基类，其中提供了一系列用来处理数组的操作，例如对数组元素进行排序、搜索数组中指定的元素等。

## 结构体

C# 中的结构体与 C/C++ 中的结构体有很大的不同，在 C# 中结构体具有以下功能：

- 结构体中可以具有方法、字段、索引、属性、运算符方法和事件；
- 结构体中可以定义构造函数，但不能定义析构函数，需要注意的是，定义的构造函数不能没有参数，因为没有参数的构造函数是 C# 默认自动定义的，而且不能更改；
- 与类不同，结构体不能继承其他结构体或类；
- 结构体不能用作其他结构体或类的基础结构；
- 一种结构体可以实现一个或多个接口；
- 结构体成员不能被设定为 abstract、virtual 或 protected；
- 与类不同，结构体可以不用 New 操作符来实例化，当使用 New 操作符来实例化结构体时会自动调用结构体中的构造函数；
- 如果不使用 New 操作符来实例化结构体，结构体对象中的字段将保持未分配状态，并且在所有字段初始化之前无法使用该结构体实例。

类和结构体的主要区别如下所示：

- 类是引用类型，结构体是值类型；
- 结构体不支持继承，但可以实现接口；
- 结构体中不能声明默认的构造函数。

## this

### 使用 this 表示当前类的对象

### 使用 this 关键字串联构造函数

```c#
public class Test
{
    public Test()
    {
        Console.WriteLine("无参构造函数");
    }
    // 先执行 Test()，后执行 Test(string text)
    public Test(string text) : this()
    {
        Console.WriteLine(text);
        Console.WriteLine("实例构造函数");
    }
}
```

### 使用 this 关键字作为类的索引器

索引器不能是静态的。

```c#
class Demo
{
    static void Main(string[] args) 
    {
        Test a = new Test();
        Console.WriteLine("Temp0:{0}, Temp1:{1}", a[0], a[1]);
    }
}

public class Test
{
    int Temp0 = 0;
    int Temp1 = 1;
    public int this[int index]
    {
        get
        {
            return (0 == index) ? Temp0 : Temp1;
        }

        set
        {
            if (0 == index)
                Temp0 = value;
            else
                Temp1 = value;
        }
    }
}
```

### 使用 this 关键字作为原始类型的扩展方法

```c#
class Demo
{
    static void Main(string[] args) 
    {
        string str = "C语言中文网";
        string newstr = str.ExpandString();
        Console.WriteLine(newstr);
    }
}
public static class Test
{
    public static string ExpandString(this string name)
    {
        return name +" http://c.biancheng.net/";
    }
}
```

## 继承

C# 中只支持单继承，也就是说一个派生类只能继承一个基类，但是继承是可以传递的。

```c#
// 基类
class Shape{
    protected int width, height;
    public void setWidth(int w){
        width = w;
    }
    public void setHeight(int h){
        height = h;
    }
}
// 派生类
class Rectangle : Shape{
    public int getArea(){
        return width*height;
    }
}
```

### 接口

C# 并不支持多重继承，但是可以借助接口来实现多重继承。

一个接口可以继承另一个接口，例如可以使用接口 1 继承接口 2，当用某个类来实现接口 1 时，必须同时实现接口 1 和接口 2 中的所有成员。

```c#
// 基类
class Shape{
    protected int width, height;
    public void setWidth(int w){
        width = w;
    }
    public void setHeight(int h){
        height = h;
    }
}
// 定义接口
public interface Perimeter{
    int getGirth();
}
// 派生类
class Rectangle : Shape, Perimeter{
    public int getArea(){
        return width*height;
    }
    public int getGirth(){
        return (width+height)*2;
    }
}
```

## 多态

### 编译时多态

C# 提供了两种技术来实现编译时多态，分别是函数重载和运算符重载。

#### 函数重载

在同一个作用域中，可以定义多个同名的函数，但是这些函数彼此之间必须有所差异，比如参数个数不同或参数类型不同等等，返回值类型不同除外。

#### 运算符重载

```c#
public static Box operator+ (Box b, Box c)
{
    Box box = new Box();
    box.length = b.length + c.length;
    box.breadth = b.breadth + c.breadth;
    box.height = b.height + c.height;
    return box;
}
```

### 运行时多态

C# 允许您使用 abstract 关键字来创建抽象类，抽象类用于实现部分接口。抽象类包含抽象方法，可以在派生类中实现。

```c#
abstract class Shape{
    public abstract int area();
}

class Rectangle : Shape{
    private int width, height;
    public Rectangle(int w, int h){
        width = w;
        height = h;
    }
    public override int area(){
        return (width * height);
    }
}
```

## 特性

特性（Attribute）是一种用于在程序运行时传递各种元素（例如类、方法、结构、枚举等）行为信息的声明性代码。使用特性可以将元数据（例如编译器指令、注释、描述、方法和类等信息）添加到程序中。.Net Framework 提供了两种类型的特性，分别是预定义特性和自定义特性。

### 预定义特性

#### AttributeUsage

#### Conditional

预定义特性 Conditional 用来标记一个方法，它的执行依赖于指定的预处理标识符。根据该特性值的不同，在编译时会起到不同的效果，例如当值为 Debug 或 Trace 时，会在调试代码时显示变量的值。

```c#
[Conditional("DEBUG")]
public static void Message(string msg)
{
    Console.WriteLine(msg);
}
```

#### Obsolete

预定义特性 Obsolete 用来标记不应被使用的程序，您可以使用它来通知编译器放弃某个目标元素。例如当您需要使用一个新方法来替代类中的某个旧方法时，就可以使用该特性将旧方法标记为 obsolete（过时的）并来输出一条消息，来提示我们应该使用新方法代替旧方法。

```c#
[Obsolete("OldMethod 已弃用，请改用 NewMethod", true)]
static void OldMethod()
{
	Console.WriteLine("已弃用的函数");
}
```

### 自定义特性

自定义特性不仅可以用于存储声明性的信息，还可以在运行时被检索。创建并使用自定义特性可以分为四个步骤：

- 声明自定义特性；
- 构建自定义特性；
- 在目标程序上应用自定义特性；
- 通过反射访问特性。

## 反射

反射（Reflection）是指程序可以访问、检测和修改它本身状态或行为的一种能力，反射中提供了用来描述程序集、模块和类型的对象，可以使用反射动态地创建类型的实例，并将类型绑定到现有对象，或者从现有对象中获取类型，然后调用其方法或访问其字段和属性。 如果代码中使用了特性，也可以利用反射来访问它们。

反射具有以下用途：

- 在运行时查看视图属性信息；
- 检查装配中的各种类型并实例化这些类型；
- 在后期绑定到方法和属性；
- 在运行时创建新类型，然后使用这些类型执行一些任务。

## 属性

属性（Property）是类（class）、结构体（structure）和接口（interface）的成员，类或结构体中的成员变量称为字段，属性是字段的扩展，使用访问器（accessors）可以读写私有字段的值。

### 访问器

属性访问器有两种，分别是 get 属性访问器和 set 属性访问器。其中 get 访问器用来返回属性的值，set 访问器用来为属性设置新值。在声明访问器时可以仅声明其中一个，也可以两个访问器同时声明。

```c#
public string Code {
   get {
      return code;
   }
   set {
      code = value;
   }
}

//抽象属性
public abstract string Name
{
    get;
    set;
}
```

## 索引器

### 定义索引器

```c#
public class Test
{
    int Temp0 = 0;
    int Temp1 = 1;
    public int this[int index]
    {
        get
        {
            return (0 == index) ? Temp0 : Temp1;
        }

        set
        {
            if (0 == index)
                Temp0 = value;
            else
                Temp1 = value;
        }
    }
}
```

### 索引器重载

索引器可以被重载，而且在声明索引器时也可以带有多个参数，每个参数可以是不同的类型。另外，索引器中的索引不必是整数，也可以是其他类型，例如字符串类型。

## 委托

C# 中的委托（Delegate）类似于 C 或 C++ 中的函数指针，是一种引用类型，表示对具有特定参数列表和返回类型的方法的引用。委托特别适用于实现事件和回调方法，所有的委托都派生自 System.Delegate 类。在实例化委托时，可以将委托的实例与具有相同返回值类型的方法相关联，这样就可以通过委托来调用方法。另外，使用委托还可以将方法作为参数传递给其他方法。

委托具有以下特点：

- 委托类似于 C/C++ 中的函数指针，但委托是完全面向对象的。另外，C++ 中的指针会记住函数，而委托则是同时封装对象实例和方法；
- 委托允许将方法作为参数进行传递；
- 委托可用于定义回调方法；
- 委托可以链接在一起，例如可以对一个事件调用多个方法；
- 方法不必与委托类型完全匹配；

### 声明委托

声明委托需要使用 delegate 关键字，语法格式如下：

```c#
delegate <return type> delegate-name(<parameter list>)
```

其中 return type 为返回值类型，delegate-name 为委托的名称，parameter list 为参数列表。

### 实例化委托

委托一旦声明，想要使用就必须使用 new 关键字来创建委托的对象，同时将其与特定的方法关联。如下例所示：

```c#
public delegate void printString(string s);      // 声明一个委托
printString ps1 = new printString(WriteToScreen);// 实例化委托对象并将其与 WriteToScreen 方法关联
printString ps2 = new printString(WriteToFile);  // 实例化委托对象并将其与 WriteToFile 方法关联
```

### 多播委托（合并委托）

委托对象有一个非常有用的属性，那就是可以通过使用`+`运算符将多个对象分配给一个委托实例，同时还可以使用`-`运算符从委托中移除已分配的对象，当委托被调用时会依次调用列表中的委托。委托的这个属性被称为委托的多播，也可称为组播，利用委托的这个属性，您可以创建一个调用委托时要调用的方法列表。

```c#
static void Main(string[] args){
    // 创建委托实例
    NumberChanger nc;
    NumberChanger nc1 = new NumberChanger(AddNum);
    NumberChanger nc2 = new NumberChanger(MultNum);
    nc = nc1;
    nc += nc2;
    // 调用多播
    nc(5);
    Console.WriteLine("num 的值为: {0}", getNum());
    Console.ReadKey();
}
```

## 事件

在C# 中，事件（Event）可以看作是用户的一系列操作，例如点击键盘的某个按键、单击/移动鼠标等，当事件发生时我们可以针对事件做出一系列的响应，例如退出程序、记录日志等等。C# 中线程之间的通信就是使用事件机制实现的。

事件需要在类中声明和触发，并通过委托与事件处理程序关联。事件可以分为发布器和订阅器两个部分，其中发布器是一个包含事件和委托的对象，事件和委托之间的联系也定义在这个类中，发布器类的对象可以触发事件，并使用委托通知其他的对象；订阅器则是一个接收事件并提供事件处理程序的对象，发布器类中的委托调用订阅器类中的方法（事件处理程序）。

有关事件我们需要注意以下几点：

- 发布器确定何时触发事件，订阅器确定对事件作出何种响应；
- 一个事件可以拥有多个订阅器，同时订阅器也可以处理来自多个发布器的事件；
- 没有订阅器的事件永远也不会触发；
- 事件通常用于定义针对用户的操作，例如单击某个按钮；
- 如果事件拥有多个订阅器，当事件被触发时会同步调用所有的事件处理程序；
- 在 .NET 类库中，事件基于 EventHandler 委托和 EventArgs 基类。

```c#
//若要在类中声明一个事件，首先需要为该事件声明一个委托类型
public delegate void delegate_name(string status);
//然后使用 event 关键字来声明事件本身
public event delegate_name event_name;
```

```c#
class Demo
{
    static void Main(string[] args){
        PublisherDemo e = new PublisherDemo(); /* 实例发布器类*/
        SubscriberDemo v = new SubscriberDemo(); /* 实例订阅器类 */
        e.MyEvent += new PublisherDemo.MyEntrust( v.printf );
        e.SetValue("C语言中文网");
    }
}
/***********发布器类***********/
public class PublisherDemo{
    private string value;
    public delegate void MyEntrust(string str);
    public event MyEntrust MyEvent;
    public void SetValue( string s ){
        value = s;
        MyEvent(value);     // 触发事件
    }
}

/***********订阅器类***********/
public class SubscriberDemo{
    public void printf(string str){
        Console.WriteLine(str);
    }
}
```

## 泛型

泛型（Generic）是一种规范，它允许我们使用占位符来定义类和方法，编译器会在编译时将这些占位符替换为指定的类型，利用泛型的这一特性我们可以定义通用类（泛型类）或方法（泛型方法）。

可以将泛型看作是一种增强程序功能的技术，泛型类和泛型方法兼具可重用性、类型安全性和效率，这是非泛型类和非泛型方法无法实现的。泛型通常与集合以及作用于集合的方法一起使用，System.Collections.Generic 命名空间下就包含几个基于泛型的集合类。下面总结了一些关于泛型的特性：

- 使用泛型类型可以最大限度地重用代码、保护类型的安全性以及提高性能；
- 泛型最常见的用途是创建集合类；
- .NET 类库在 System.Collections.Generic 命名空间中包含几个新的泛型集合类，您可以使用这些类来代替 System.Collections 中的集合类；
- 您可以创建自己的泛型接口、泛型类、泛型方法、泛型事件和泛型委托；
- 您也可以对泛型类进行约束以访问特定数据类型的方法；
- 在泛型数据类型中所用类型的信息可在运行时通过使用反射来获取。

### 泛型类

```c#
// 定义泛型类
class GenericClass<T>{
    // 泛型方法
    public GenericClass(T msg){
        Console.WriteLine(msg);
    }
}
class Demo
{
    static void Main(string[] args){
        GenericClass<string> str_gen = new GenericClass<string>("C语言中文网");
        GenericClass<int> int_gen = new GenericClass<int>(1234567);
        GenericClass<char> char_gen = new GenericClass<char>('C');
        Console.ReadKey();
    }
}
```

### 泛型方法

```c#
static void Swap<T>(ref T lhs, ref T rhs)
{
    T temp;
    temp = lhs;
    lhs = rhs;
    rhs = temp;
}

Swap<int>(ref a, ref b);
Swap<char>(ref c, ref d);
```

### 泛型委托

```c#
delegate T NumberChanger<T>(T n);
```

## 匿名函数

可以将匿名函数简单的理解为没有名称只有函数主体的函数。匿名函数提供了一种将代码块作为委托参数传递的技术，它是一个“内联”语句或表达式，可在任何需要委托类型的地方使用。匿名函数可以用来初始化命名委托或传递命名委托作为方法参数。

### 语法

```c#
//匿名函数是通过使用 delegate 关键字创建的委托实例来声明的
delegate void NumberChanger(int n);
NumberChanger nc = delegate(int x)
{
  Console.WriteLine("Anonymous Method: {0}", x);
};
```

## 多线程

多线程就是多个线程同时工作的过程，我们可以将线程看作是程序的执行路径，每个线程都定义了一个独特的控制流，用来完成特定的任务。如果您的应用程序涉及到复杂且耗时的操作，那么使用多线程来执行是非常有益的。使用多线程可以节省 CPU 资源，同时提高应用程序的执行效率，例如现代操作系统对并发编程的实现就用到了多线程。到目前为止我们编写的示例程序都是单线程的应用程序，这样的应用程序一次只能执行一个任务。











