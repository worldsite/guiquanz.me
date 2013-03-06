--- 
layout: post
title: Google Go编程基础
date: 2012-12-18
categories:
  - 技术
tags:
  - go
---

## 背景介绍

类型：转载（`进行了扩展编辑`）

作者：Samuel Tesla  

译者: 黄璜 

中文源地址： [Google Go：初级读本](http://www.infoq.com/cn/articles/google-go-primer)

英文源地址： [Google Go: A Primer](http://www.infoq.com/articles/google-go-primer)

![](/img/article/golang/go-logo.jpg)

Go语言的设计者计划设计一门简单、高效、安全和 并发的语言。这门语言简单到甚至不需要有一个符号表来进行词法分析。它可以快速地编译；整个工程的编译时间在秒以下的情况是常事。它具备垃圾回收功能，因 此从内存的角度是安全的。它进行静态类型检查，并且不允许自动类型转换（类型提升等），因而对于类型而言是安全的。同时语言还内建了强大的并发实现机制。


## 阅读Go

Go的语法传承了与C一样的风格。程序由函数组成，而函数体是一系列的语句序列。一段代码块用花括号括起来。仅保留了有限的关键字。表达式使用同样的中缀运算符。语法上并无太多出奇之处。

Go语言的作者，在设计这一语言时坚持一个单一的指导原则：__简单明了至上__。一些新的语法构件提供了简明地表达一些约定俗成的概 念的方式，相较之下用C表达显得冗长。而其他方面则是针对几十年的使用所呈现出来的一些不合理的语言选择作出了改进。


## 编程基础

### 变量声明

变量声明，如下：
<pre class="prettyprint linenums">
var sum int // 简单声明
var total int = 42 // 声明并初始化
</pre>

最值得注意的是，这些声明里的类型跟在变量名的后面。乍一看有点怪，但这更清晰明了。比如，以下面这个C片段来说：
<pre class="prettyprint linenums">
int* a, b;
</pre>
它并列声明了变量，但这里实际的意思是a是一个指针，但b不是。如果要将两者都声明为指针，必须要重复星号。然后在Go语言里，通过如下方式可以将两者都声明为指针：
<pre class="prettyprint linenums">
var a, b *int
</pre>

如果一个变量初始化了，编译器通常能推断它的类型，所以程序员不必显式的敲出来：
<pre class="prettyprint linenums">
var label = "name"
</pre>
然而，在这种情况下var几乎显得是多余了。因此，Go的作者引入了一个新的运算符来 声明和初始化一个新的变量：
<pre class="prettyprint linenums">
name := "Samuel"
</pre>


### 条件语句

Go语言当中的条件句与C当中所熟知的if-else构造一样，但条件不需要被打包在括号内。这样可以减少阅读代码时的视觉上的混乱。

__括号并不是唯一被移去的视觉干扰__。在条件之间可以包括一个简单的语句，如下的代码：

<pre class="prettyprint linenums">
result := someFunc();
if result > 0 {
	/* Do something */
} else {
	/* Handle error */
}
</pre>

可以被精简成：

<pre class="prettyprint linenums">
if result := someFunc(); result > 0 { 
	/* Do something */
} else {
	/* Handle error */
}
</pre>

然而，在后面这个例子当中，result只在条件块内部有效——而前者中，它在整个包含它的上下文中都是可存取的。


### 分支语句

分支语句同样是似曾相识，但也有增强。像条件语句一样，它允许一个简单的语句位于分支的表达式之前。然而，他们相对于在C语言中的分支而言走得更远。

首先，为了让分支跳转更简明，作了两个修改。`case`可以是逗号分隔的列表，而fall-throuth也不再是默认的行为。

因此，如下的C代码：
<pre class="prettyprint linenums">
int result;
switch (byte) {
 case 'a':
 case 'b':
   {
     result = 1
     break
   }

 default:
   result = 0
}
</pre>

在Go里就变成了这样：

<pre class="prettyprint linenums">
var result int
switch byte {
case 'a', 'b':
  result = 1
default:
  result = 0
}
</pre>

第二点，Go的分支跳转可以匹配比整数和字符更多的内容，任何有效的表达式都可以作为跳转语句值。只要它与分支条件的类型是一样的。

因此如下的C代码：

<pre class="prettyprint linenums">
int result = calculate();
if (result < 0) {
  /* negative */
} else if (result > 0) {
  /* positive */
} else {
  /* zero */
}
</pre>

在Go里可以这样表达：

<pre class="prettyprint linenums">
switch result := calculate(); true {
case result < 0:
  /* negative */
case result > 0:
  /* positive */
default:
  /* zero */
}
</pre>

这些都是公共的约定俗成，比如如果分支值省略了，就是默认为真，所以上面的代码可以这样写：

<pre class="prettyprint linenums">
switch result := calculate(); {
case result < 0:
  /* negative */
case result > 0:
  /* positive */
default:
  /* zero */
}
</pre>


### 循环

Go只有一个关键字用于引入循环。但它提供了除do-while外C语言当中所有可用的循环方式。

#### 条件

<pre class="prettyprint linenums">
for a > b { /* ... */ }
</pre>

#### 初始，条件和步进

<pre class="prettyprint linenums">
for i := 0; i < 10; i++ { /* ... */ }
</pre>

#### 范围

range语句右边的表达式必须是array，slice，string或者map， 或是指向array的指针，也可以是channel。
<pre class="prettyprint linenums">
for i := range "hello" { /* ... */ }
</pre>

### 无限循环

for { /* ever */ }


## 函数

声明函数的语法与C不同。像变量声明一样，类型是在它们所描述的术语之后声明的。在C语言中：
<pre class="prettyprint linenums">
int add(int a, b) { return a + b }
</pre>

在Go里面是这样描述的：

<pre class="prettyprint linenums">
func add(a, b int) int { return a + b }
</pre>

#### 多返回值

在C语言当中常见的做法是保留一个返回值来表示错误(比如，read()返回0)，或 者保留返回值来通知状态，并将传递存储结果的内存地址的指针。这容易产生了不安全的编程实践，因此在像Go语言这样有良好管理的语言中是不可行的。

认识到这一问题的影响已超出了函数结果与错误通讯的简单需求的范畴，Go的作者们在语言中内建了函数返回多个值的能力。

作为例子，这个函数将返回整数除法的两个部分：
<pre class="prettyprint linenums">
func divide(a, b int) (int, int) {
  quotient := a / b
  remainder := a % b
  return quotient, remainder
}
</pre>

有了多个返回值，有良好的代码文档会更好——而Go允许你给返回值命名，就像参数一样。你可以对这些返回的变量赋值，就像其它的变量一样。所以我们可以重写divide：

<pre class="prettyprint linenums">
func divide(a, b int) (quotient, remainder int) {
  quotient = a / b
  remainder = a % b
  return
}
</pre>

多返回值的出现促进了"comma-ok"的模式。有可能失败的函数可以返回第二个布尔结果来表示成功。作为替代，也可以返回一个错误对象，因此像下面这样的代码也就不见怪了：
<pre class="prettyprint linenums">
if result, ok := moreMagic(); ok {
  /* Do something with result */
}
</pre>


### 匿名函数

有了垃圾收集器意味着为许多不同的特性敞开了大门——其中就包括匿名函数。Go为声明匿名函数提供了简单的语法。像许多动态语言一样，这些函数在它们被定义的范围内创建了词法闭包。

考虑如下的程序：
<pre class="prettyprint linenums">
func makeAdder(x int) (func(int) int) {
  return func(y int) int { return x + y }
}

func main() {
  add5 := makeAdder(5)
  add36 := makeAdder(36)
  fmt.Println("The answer:", add5(add36(1))) //=> The answer: 42
}
</pre>


### 基本类型

像C语言一样，Go提供了一系列的基本类型，常见的`布尔`，`整数`和`浮点数`类型都具备。它有一个Unicode的字符串类型和数组类型。同时该语言还引入了两种新的类型：`slice`和`map`。

#### 数组和切片

Go语言当中的数组不是像C语言那样动态的。它们的大小是类型的一部分，在编译时就决定了。数组的索引还是使用的熟悉的C语法(如 a[i])，并且与C一样，索引是由0开始的。编译器提供了内建的功能在编译时求得一个数组的长度 (如 len(a))。如果试图超过数组界限写入，会产生一个运行时错误。

Go还提供了切片（slices），作为数组的变形。`一个切片(slice)表示一个数组内的连续分段，支持程序员指定底层存储的明确部分`。构建一个切片 的语法与访问一个数组元素类似：

<pre class="prettyprint linenums">
/* Construct a slice on ary that starts at s and is len elements long */
s1 := ary[s:len]

/* Omit the length to create a slice to the end of ary */
s2 := ary[s:]

/* Slices behave just like arrays */
s[0] == ary[s] //=> true

// Changing the value in a slice changes it in the array
ary[s] = 1
s[0] = 42
ary[s] == 42 //=> true
</pre>

该切片所引用的数组分段可以通过将新的切片赋值给同一变量来更改：

<pre class="prettyprint linenums">
/* Move the start of the slice forward by one, but do not move the end */
s2 = s2[1:]

/* Slices can only move forward */
s2 = s2[-1:] // this is a compile error
</pre>

`切片的长度可以更改，只要不超出切片的容量。切片s的容量是数组从s[0]到数组尾端的大小，并由内建的cap()函数返回。一个切片的长度永远不能超出它的容量`。

这里有一个展示长度和容量交互的例子：

<pre class="prettyprint linenums">
a := [...]int{1,2,3,4,5} // The ... means "whatever length the initializer has"
len(a) //=> 5

/* Slice from the middle */
s := a[2:4] //=> [3 4]
len(s), cap(s) //=> 2, 3

/* Grow the slice */
s = s[0:3] //=> [3 4 5]
len(s), cap(s) //=> 3, 3

/* Cannot grow it past its capacity */
s = s[0:4] // this is a compile error
</pre>

通常，一个切片就是一个程序所需要的全部了，在这种情况下，程序员根本用不着一个数组，Go有两种方式直接创建切片而不用引用底层存储：
<pre class="prettyprint linenums">
/* literal */
s1 := []int{1,2,3,4,5}

/* empty (all zero values) */
s2 := make([]int, 10) // cap(s2) == len(s2) == 10
</pre>

### Map类型

几乎每个现在流行的动态语言都有的数据类型，但在C中不具备的，就是dictionary。Go提供了一个基本的dictionary类型叫做map。下 面的例子展示了如何创建和使用Go map：
<pre class="prettyprint linenums">
m := make(map[string] int) // A mapping of strings to ints

/* Store some values */
m["foo"] = 42
m["bar"] = 30

/* Read, and exit program with a runtime error if key is not present. */
x := m["foo"]

/* Read, with comma-ok check; ok will be false if key was not present. */
x, ok := m["bar"]

/* Check for presence of key, _ means "I don't care about this value." */
_, ok := m["baz"] // ok == false

/* Assign zero as a valid value */
m["foo"] = 0;
_, ok := m["foo"] // ok == true

/* Delete a key */
m["bar"] = 0, false
_, ok := m["bar"] // ok == false
</pre>


### 面向对象

Go语言支持类似于C语言中使用的面向对象风格。数据被组织成structs，然后定义操作这些structs的函数。类似于Python，Go语言提供 了定义函数并调用它们的方式，因此语法并不会笨拙。

#### Struct类型

定义一个新的struct类型很简单：

<pre class="prettyprint linenums">
type Point struct {
  x, y float64
}
</pre>

现在这一类型的值可以通过内建的函数new来分配，这将返回一个指针，指向一块内存单元，其所占内存槽初始化为零。
<pre class="prettyprint linenums">
var p *Point = new(Point)
p.x = 3
p.y = 4
</pre>

这显得很冗长，而Go语言的一个目标是尽可能的简明扼要。所以提供了一个同时分配和初始化struct的语法：
<pre class="prettyprint linenums">
var p1 Point = Point{3,4}  // Value
var p2 *Point = &Point{3,4} // Pointer
</pre>


#### 方法

一旦声明了类型，就可以将该类型显式的作为第一个参数来声明函数：

<pre class="prettyprint linenums">
func (self Point) Length() float {
  return math.Sqrt(self.x*self.x + self.y*self.y);
}
</pre>

这些函数之后可作为struct的方法而被调用：

<pre class="prettyprint linenums">
p := Point{3,4}
d := p.Length() //=> 5
</pre>

方法实际上既可以声明为值也可以声明为指针类型。Go将会适当的处理引用或解引用对象，所以既可以对类型T，也可以对类型*T声明方式，并合理地使用它们。

让我们为Point扩展一个变换器：

<pre class="prettyprint linenums">
/* Note the receiver is *Point */
func (self *Point) Scale(factor float64) {
  self.x = self.x * factor
  self.y = self.y * factor
}
</pre>

然后我们可以像这样调用：

<pre class="prettyprint linenums">
p.Scale(2);
d = p.Length() //=> 10
</pre>

很重要的一点是理解传递给MoveToXY的self和其它的参数一样，并且是值传递，而不是引用传递。如果它被声明为Point，那么在方法内修改的struct就不再跟调用方的一样——值在它们传递给方法的时候被 拷贝，并在调用结束后被丢弃。

#### 接口

像Ruby这样的动态语言所强调面向对象编程的风格认为对象的行为比哪种对象是动态类型（duck typing）更为重要。Go所 带来的一个最强大的特性之一就是提供了可以在编程时运用动态类型的思想而把行为定义的合法性检查的工作推到编译时。这一行为的名字被称作接口。

定义一个接口很简单：

<pre class="prettyprint linenums">
type Writer interface {
  Write(p []byte) (n int, err os.Error)
}
</pre>

这里定义了一个接口和一个写字节缓冲的方法。任何实现了这一方法的对象也实现了这一接口。不需要像Java一样进行声明，编译器能推断出来。这既给予了动态类型的表达能力又保留了静态类型检查的安全。

Go当中接口的运作方式支持开发者在编写程序的时候发现程序的类型。如果几个对象间存在公共行为，而开发者想要抽象这种行为，那么它就可以创建一个接口并使用它。

考虑如下的代码：

<pre class="prettyprint linenums">
// Somewhere in some code:
type Widget struct {}
func (Widget) Frob() { /* do something */ }

// Somewhere else in the code:
type Sprocket struct {}
func (Sprocket) Frob() { /* do something else */ }

/* New code, and we want to take both Widgets and Sprockets and Frob them */
type Frobber interface {
  Frob()
}

func frobtastic(f Frobber) { f.Frob() }
</pre>

需要特别指出的很重要的一点就是所有的对象都实现了这个空接口：
<<pre class="prettyprint linenums">>
interface {}
</pre>

#### 继承

Go语言不支持继承，至少与大多数语言的继承不一样。并不存在类型的层次结构。相较于继承，Go鼓励使用组合和委派，并为此提供了相应的语法甜点使其更容易接受。

有了这样的定义：

<pre class="prettyprint linenums">
type Engine interface {
  Start()
  Stop()
}

type Car struct {
  Engine
}
</pre>

于是我可以像下面这样编写：

<pre class="prettyprint linenums">
func GoToWorkIn(c Car) {
  /* get in car */

  c.Start();

  /* drive to work */

  c.Stop();

  /* get out of car */
}
</pre>

当我声明Car这个struct的时候，我定义了一个匿名成员。这是一 个只能被其类型识别的成员。匿名成员与其它的成员一样，并有着和类型一样的名字。因此我还可以写成c.Engine.Start()。 如果Car并没有其自身方法可以满足调用的话,编译器自动的会将在Car上的调用委派给它的Engine上面的方法。

由匿名成员提供的分离方法的规则是保守的。如果为一个类型定义了一个方法，就使用它。如果不是，就使用为匿名成员定义的方法。如果有两个匿名成员都提供一 个方法，编译器将会报错，但只在该方法被调用的情况下。

这种组合是通过委派来实现的，而不是继承。一旦匿名成员的方法被调用，控制流整个都被委派给了该方法。所以你无法做到和下面的例子一样来模拟类型层次：

<pre class="prettyprint linenums">
type Base struct {}
func (Base) Magic() { fmt.Print("base magic") }
func (self Base) MoreMagic() { 
  self.Magic()
  self.Magic()
}

type Foo struct {
  Base
}

func (Foo) Magic() { fmt.Print("foo magic") }
</pre>

当你创建一个Foo对象时，它将会影响Base的两个方法。然而，当你调用MoreMagic时， 你将得不到期望的结果：

<pre class="prettyprint linenums">
f := new(Foo)
f.Magic() //=> foo magic
f.MoreMagic() //=> base magic base magic
</pre>

#### 并发

Go的作者选择了消息传递模型来作为推荐的并发编程方法。该语言同样支持共享内存，然后作者自有道理：

不要通过共享内存来通信，相反，通过通信来共享内存。

__该语言提供了两个基本的构件来支持这一范型：`goroutines`和`channels`__。


### Go例程

Goroutine是轻量级的并行程序执行路径，与线程，coroutine或者进程类似。然而，它们彼此相当不同，因此Go作者决定给它一个新的名字并 放弃其它术语可能隐含的意义。

创建一个goroutine来运行名为DoThis的函数十分简单：

<pre class="prettyprint linenums">
go DoThis() // but do not wait for it to complete
</pre>

匿名的函数可以这样使用：

<pre class="prettyprint linenums">
go func() {
  for { /* do something forever */ }
}() // Note that the function must be invoked
</pre>

这些goroutine将会通过Go运行时而映射到适当的操作系统原语（比如，POSIX线程）。


### 通道类型

有了goroutine，代码的并行执行就容易了。然而，它们之间仍然需要通讯机制。Channel提供一个FIFO通信队列刚好能达到这一目的。

以下是使用channel的语法：

<pre class="prettyprint linenums">
/* Creating a channel uses make(), not new - it was also used for map creation */
ch := make(chan int)

/* Sending a value blocks until the value is read */
ch <- 4

/* Reading a value blocks until a value is available */
i := &lt;-ch
</pre>

举例来说，如果我们想要进行长时间运行的数值计算，我们可以这样做：

<pre class="prettyprint linenums">
ch := make(chan int)

go func() {
  result := 0
  for i := 0; i < 100000000; i++ {
    result = result + i
  }
  ch <- result
}()

/* Do something for a while */

sum := &lt;-ch // This will block if the calculation is not done yet
fmt.Println("The sum is:", sum)
</pre>

channel的阻塞行为并非永远是最佳的。该语言提供了两种对其进行定制的方式：

程序员可以指定缓冲大小——想缓冲的channel发送消息不会阻塞，除非缓冲已满，同样从缓冲的channel读取也不会阻塞，除非缓冲是空的。该语言同时还提供了不会被阻塞的发送和接收的能力，而操作成功是仍然要报告。
<pre class="prettyprint linenums">
/* Create a channel with buffer size 5 */
ch := make(chan int, 5)

/* Send without blocking, ok will be true if value was buffered */
ok := ch &lt;- 42

/* Read without blocking, ok will be true if a value was read */
val, ok := &lt;-ch
</pre>


### 包

Go提供了一种简单的机制来组织代码：包。每个文件开头都会声明它属于哪一个包，每个文件也可以引入它所用到的包。任何首字母大写的名字是由包导出的，并可以被其它的包所使用。

以下是一个完整的源文件：

<pre class="prettyprint linenums">
package geometry

import "math"

/* Point is capitalized, so it is visible outside the package. */

type Point struct {

  /* the fields are not capitalized, so they are not visible
     outside of the package */

  x, y float64 
}

/* These functions are visible outside of the package */

func (self Point) Length() float64 {
  /* This uses a function in the math package */
  return math.Sqrt(self.x*self.x + self.y*self.y)
}

func (self *Point) Scale(factor float64) {
  self.setX(self.x * factor)
  self.setY(self.y * factor)
}

/* These functions are not visible outside of the package, but can be
   used inside the package */

func (self *Point) setX(x float64) { self.x = x }
func (self *Point) setY(y float64) { self.y = y }
</pre>

### Go的缺失

Go语言的作者试图将代码的清晰明确作为设计该语言作出所有决定的指导思想。第二个目标是生产一个编译速度很快的语言。有了这两个标准作为方向，来 自其它语言的许多特性就不那么适合了。许多程序员会发现他们最爱的语言特性在Go当中不存在，确实，有很多人也许会觉得Go语言由于缺乏其它语言所共有的 一些特性，还不太可用。

这当中两个缺失的特性就是异常和泛型，两者在其它语言当中都是非常有用的。而它们目前都不是Go的一分子。但因为该 语言仍处于试验阶段，它们有可能最终会加入到语言里。然而，如果将Go与其它语言作比较的话，我们应当记住Go是打算在系统编程层面作为C语言的替代。明 白这一点的话，那么缺失的这许多特性倒也不是很大的问题了。

最后，因为这一语言才刚刚发布，因此它没有什么类库或工具可以用，也没有Go语 言的集成编程环境。Go语言标准库有些有用的代码，但这与更为成熟的语言比 起来仍还是很少的。


## 扩展阅读

1、[Go语言中文翻译项目](http://code.google.com/p/golang-china)；

2、Go语言官方网站：[http://golang.org](http://golang.org/);

3、Godoc本地化翻译: [http://code.google.com/p/go-zh/](http://code.google.com/p/go-zh/);

4、Go语言中文论坛: [http://bbs.golang-china.org/](http://bbs.golang-china.org/);

5、Go语言中文Wiki: [http://wiki.golang-china.org/](http://wiki.golang-china.org/);

6、Go语言中文官网: [http://golang-china.org/](http://golang-china.org/);

7、Go语言中文IRC: [irc.freenode.net #golang-china](irc.freenode.net #golang-china);

8、Go语言视频(优酷)：[http://u.youku.com/golangchina](http://u.youku.com/golangchina);

9、[Go资源大荟萃（很齐全的内容）](http://go-lang.cat-v.org/)；

10、[golang与node.js的http模块性能对比测试（go1）](http://www.cnblogs.com/QLeelulu/archive/2012/08/12/2635261.html);

11、[Go 语言简介（上）— 语法](http://coolshell.cn/articles/8460.html);

12、[Go 语言简介（下）— 特性](http://coolshell.cn/articles/8489.html);


## 相关图书

1、樊虹剑（fango）的[《Go语言·云动力》](http://www.ituring.com.cn/book/1040)；

2、许式伟的 [《Go语言编程》](http://www.ituring.com.cn/book/967)；

3、Ivo Balbaert的[《The Way To Go》](https://sites.google.com/site/thewaytogo2012/);

4、Asta的开源书籍 [《Go Web编程》](https://github.com/astaxie/build-web-application-with-golang/blob/master/preface.md)；

5、Jan Newmarch的[Network programming with Go](http://jan.newmarch.name/go/);


## 相关开源项目

1、Go Web框架

（1）、[Golanger Web Framework](https://github.com/golangers/framework);

（2）、[一步一步学习Revel Web开源框架](http://www.cnblogs.com/ztiandan/archive/2013/01/17/2864498.html)；

2、分布式数据处理

（1）、[doozer](https://github.com/ha/doozer);

（2）、[doozerd](https://github.com/ha/doozerd);

（3）、[skynet](https://github.com/skynetservices/skynet);

3、[GitHub上更多Go项目](https://github.com/search?l=Go&p=1&q=go&ref=searchbar&type=Repositories);


