## 变量

### 关键字 val 和  var

Kotlin 中定义一个变量，**只允许在变量前声明两种关键字：val和 var**。

- val（value的简写）用来声明一个不可变的变量，这种变量在初始赋值之后就再也不能重新赋 值，对应Java中的 final 变量。
- var（variable的简写）用来声明一个可变的变量，这种变量在初始赋值之后仍然可以再被重新 赋值，对应Java中的非final变量。

为什么 kotlin 要这么设计呢？一个好的编程习惯是，除非一个变量明确允许被修改，否则都应该给它加上 final关键字。实际上，至少有90%的Java程序员没有主动在 变量前加上final关键字的意识，仅仅因为Java对此是不强制的。因此，Kotlin在设计的时候就 采用了和Java完全不同的方式，提供了val和var这两个关键字，必须由开发者主动声明该变量 是可变的还是不可变的。

那么我们应该什么时候使用val，什么时候使用var呢？这里我告诉你一个小诀窍，就是永远优先使用val来声明一个变量，而当val没有办法满足你的需求时再使用var。这样设计出来的程 序会更加健壮，也更加符合高质量的编码规范。



### 常量

```
// 声明编译时常量
private const val TAG = "MainActivity"
```



### 类型推导机制

在只用 val 和 var 定义变量的情况下，编译器使用**类型推导机制**来获取变量类型。

```kotlin
// a被赋值为10，被自动推导为整形变量
val a = 10
```

但是，在对变量延迟赋值的情况下，Kotlin 无法自动推导类型，我们想要显示声明类型。

```kotlin
// 显式地声明了变量a为Int类型，编译器不会再进行类型推导
val a: Int = 10
```





### 区间

`..`是创建两端闭区间的关键字，在`..`的两边指定区间的左右端点就可以创建一个区间了。

```kotlin
// 创建闭区间 [0,10]
val range = 0..10
```

Kotlin中可以使用`until`关键字来创建一个左闭右开的区间

```kotlin
// 创建左闭右开区间 [0,10)
val range = 0 until 10
```

同时，Kotlin 还可以创建降序区间

```kotlin
// 创建左闭右开降序区间 [10,0)
val range = 10 downto 0
```



## 函数

### 函数语法规则

函数定义语法规则如下：

```
fun methodName(param1: Int, param2: Int): Int { 
    return 0 
}
```

其中，参数括号后面的那部分是可选的，用于声明该函数会返回什么类型的数据，上述示例就表示该 函数会返回一个Int类型的数据。如果你的函数不需要返回任何数据，这部分可以直接不写。

作为一种一级结构，**函数也属于数据类型**，因此，我们可以将函数存储在变量中、将函数传递到函数，以及从函数返回函数。



### 函数引用符

如需将函数作为值引用，可以使用函数引用运算符 (`::`)。

给出一个将函数存储在变量中的案例：

```kotlin
fun main() {
    val trickFunction = ::trick
    trickFunction()
}

fun trick() {
    println("No treats!")
}
```





### 高阶函数

如果一个函数会返回或接受另一个函数作为实参，该函数就称为高阶函数。





### 单行代码函数语法糖

当一个函数中只有一行代码时，Kotlin 允许我们不必编写函数体，可以直接将唯一的一行代码写在函数定义的尾部，中间用等号连接即可。

```kotlin
// 常规写法
fun largerNumber(num1: Int, num2: Int): Int { 
    return max(num1, num2) 
} 

// 等价写法(使用语法糖)，等号足以表达返回值的意思
fun largerNumber(num1: Int, num2: Int): Int = max(num1, num2) 

// max(num1, num2)返回值类型为Int，类型推导 largerNumber的返回值类型
fun largerNumber(num1: Int, num2: Int) = max(num1, num2) 
```







## 程序逻辑控制

### if 条件语句

Kotlin中的if语句相比于Java有一个额外的功能，它是**可以有返回值的**，返回值就是if语句每
一个条件中最后一行代码的返回值。

```kotlin
fun largerNumber(num1: Int, num2: Int): Int { 
    var value = 0 
    if (num1 > num2) { 
        value = num1 
    } else { 
        value = num2 
    } 
    return value 
} 

// 简化写法
// if语句使用每个条件的最后一行代码作为返回值，并将返回值赋值给了value变量
fun largerNumber(num1: Int, num2: Int): Int { 
    val value = if (num1 > num2) { 
        num1 
    } else { 
        num2 
    } 
    return value 
} 

// 使用函数语法糖进一步精简
// 当一个函数只有一行代码时，可以省略函数体部分，直接将这一行代码使用等号串连在函数定义的尾部
fun largerNumber(num1: Int, num2: Int) = if (num1 > num2) { 
    num1 
} else { 
    num2 
} 

// 简化写法
// 如果正文仅包含一个返回值或表达式，您可以移除大括号，使代码更简洁
fun largerNumber(num1: Int, num2: Int) = if (num1 > num2) num1 else num2
```



### when 条件语句

Kotlin中的 when 语句有点类似于Java中的switch语句，但它又远比switch语句强大得多。when语句允许传入一个任意类型的参数，然后可以在when的结构体中定义一系列的条件，格式是：

```kotlin
when(任意类型参数){
	匹配值 -> { 执行逻辑 } 
	匹配值 -> { 执行逻辑 } 
	else -> { 执行逻辑 }   // 确保不会出现未给变量赋值的情况
}
```

当执行逻辑只有一行代码时，`{ }` 可以省略。同时，我们可以使用英文逗号 (`,`)、`in` 关键字和 `is` 关键字来组成更复杂的 `when` 条件，例如**使用英文逗号 (`,`) 处理多个条件**。

**(1) 精准匹配**

when **支持精准匹配**。

```kotlin
// 精准匹配
fun getScore(name: String) = when (name) { 
    "Tom" -> 86 
    "Jim" -> 77 
    "Jack" -> 95 
    "Lily" -> 100 
    else -> 0 
}

// when不带参数的写法，将判断的表达式完整地写在when的结构体当中
fun getScore(name: String) = when { 
    name == "Tom" -> 86 
    name == "Jim" -> 77 
    name == "Jack" -> 95 
    name == "Lily" -> 100 
    else -> 0 
}

```

这种无参数的when语句写起来比较冗余，但**有些场景必须使用这种无参数的when语句写法才能实现**。举个 例子，假设所有名字以Tom开头的人，他的分数都是86分，这种场景如果用带参数的when语句 来写就无法实现，而使用不带参数的when语句就可以这样写：

```kotlin
fun getScore(name: String) = when { 
    name.startsWith("Tom") -> 86 
    name == "Jim" -> 77 
    name == "Jack" -> 95 
    name == "Lily" -> 100 
    else -> 0 
} 
```

**(2) 类型匹配**

when **也支持类型匹配**。其中，is关键字就是类型匹配的核心，它相当于Java中的 instanceof 关键字。

```kotlin
// 类型匹配
// Number是Kotlin内置的一个抽象类，像Int、Long、Float、Double等与数字相关的类都是它的子类
fun checkNumber(num: Number) { 
    when (num) { 
        is Int -> println("number is Int") 
        is Double -> println("number is Double") 
        else -> println("number not support") 
    } 
}
```



### 循环语句

**(1) while 语句**

while循环不管是在语法还是使用技巧上都和Java中的 while循环没有任何区别

**(2) for 语句**

Kotlin在for循环方面做了很大幅度的修改：

- Java中最常用的for-i循环在Kotlin中直接被舍弃 了
- 而 Java 中另一种for-each循环则被Kotlin进行了大幅度的加强，变成了for-in循环。

for-in 循环可以遍历区间、数组和集合。for-in 循环对区间遍历的案例如下：

```kotlin
// 在遍历[0, 10)这个区间的时候，每次执行循环都会在区间范围内递增1
 for (i in 0 until 10) { 
        println(i) 
 } 

// 在遍历[10,0)这个区间的时候，每次执行循环都会在区间范围内递减1
 for (i in 0 until 10) { 
        println(i) 
 } 

// 使用 step 关键字
// 在遍历[0, 10)这个区间的时候，每次执行循环都会在区间范围内递增2
 for (i in 0 until 10 step 2) { 
        println(i) 
 } 

// 遍历 map
 val map = mapOf("Apple" to 1, "Banana" to 2, "Orange" to 3, "Pear" to 4) 
 for ((fruit, number) in map) { 
        println("fruit is " + fruit + ", number is " + number) 
 } 

```

（3）高阶函数

[`repeat()`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/repeat.html) 高阶函数是使用函数表达 `for` 循环的简洁方式。`repeat()` 函数具有以下函数签名：

```kotlin
repeat(times: Int, action: (Int) -> Unit)
```

`times` 参数是操作应发生的次数。`action` 参数是一个函数，它接受单个 `Int` 参数并返回 `Unit` 类型的函数。



## 面向对象编程





### 接口

Kotlin 是单继承结构的语言，任何一个类最多只能继承一个父类，但是却可以实现任意多个接口。为Kotlin中的接口部分和 Java 几乎是完全一致的。

```kotlin
interface Study { 
    // 强制要求实现类重写
    fun readBooks() 
    
    // 默认函数，不强制实现类要求重写
    fun doHomework() { 
        println("do homework default implementation.") 
    } 
} 


class Student(name: String, age: Int) : Person(name, age), Study { 
    override fun readBooks() { 
        println(name + " is reading.") 
    }  
} 
```

关键细节如下：

- Java 中继承使用的关键字是 extends，实现接口使用的关键字是 implements，而 Kotlin 中统一使用冒号，中间用逗号进行分隔。
- Kotlin 中使用 override 关键字来重写父类或者实现接口中的函数。



### 可见性修饰符

Kotlin 抛弃了 Java 中的 default 可 见性（同一包路径下的类可见），引入了一种新的可见性概念，只对同一模块中的类可见，使用的是 internal 修饰符。

```
// 为属性指定可见性修饰符
modifier var name : data_type = initial value

// 为方法指定可见性修饰符
modifier fun name() {
	body
}

// 为构造函数指定可见性修饰符
class name modifier constructor(parameters) {
	body
}

// 为类指定可见性修饰符
modifier class name() {
	body
}
```



> **注意**：[模块*module*](https://developer.android.google.cn/studio/projects?hl=zh-cn#ApplicationModules)是源文件和构建设置的集合，可让您将项目划分为独立的功能单元。您的项目可以包含一个或多个模块。您可以独立构建、测试和调试每个模块。
>
> 软件包就像是用来对相关类进行分组的目录或文件夹，模块则是用来为应用的源代码、资源文件和应用级设置提供容器。一个模块可以包含多个软件包。

| **修饰符**  | **可在相同类中访问** | **可在子类中访问** | **可在相同模块中访问** | **可在模块之外访问** |
| ----------- | -------------------- | ------------------ | ---------------------- | -------------------- |
| `private`   | ✔                    | 𝗫                  | 𝗫                      | 𝗫                    |
| `protected` | ✔                    | ✔                  | 𝗫                      | 𝗫                    |
| `internal`  | ✔                    | ✔                  | ✔                      | 𝗫                    |
| `public`    | ✔                    | ✔                  | ✔                      | ✔                    |









## Lambda 编程

### Lambda 表达式语法

Lambda表达式的语法结构：

```
{参数名1: 参数类型, 参数名2: 参数类型 -> 函数体} 
```

这是Lambda表达式最完整的语法结构定义。首先最外层是一对大括号，如果有参数传入到 Lambda表达式中的话，我们还需要声明参数列表，**参数列表的结尾使用一个`->`符号**，表示参数列表的结束以及函数体的开始，函数体中可以编写任意行代码（虽然不建议编写太长的 码），并且**最后一行代码会自动作为Lambda表达式的返回值**。



### 使用 lambda 表达式重新定义函数

**lambda 表达式提供了简洁的语法来定义函数，无需使用 `fun` 关键字**。我们可以直接将 lambda 表达式存储在变量中，无需对其他函数进行函数引用。

在赋值运算符 (`=`) 前面，您要添加 `val` 或 `var` 关键字，后跟变量名称，以供您在调用函数时使用。赋值运算符 (`=`) 后面是 lambda 表达式，它由一对大括号构成，而大括号中的内容则构成函数体。语法结构为：

```
val 变量名 = {
	函数体
}
```

相应的使用案例为：

```kotlin
fun main() {
    val trickFunction = trick
    trick()	// 输出 No treats!
}

val trick = {
    println("No treats!")
}
```





### Lambda 使用案例

**(1) 将函数存储在变量中**

```kotlin
fun main() {
    val trickFunction = trick
    trick()	// 输出 No treats!
}

val trick = {
    println("No treats!")
}
```



**(2) 将函数用作数据类型**

```
// 将 treat 变量的数据类型指定为 () -> Unit
// 圆括号为空，因为函数没有任何参数。返回值类型为 Unit，因为函数不返回任何内容。
val treat: () -> Unit = {
    println("Have a treat!")
}

fun main() {
    treat()
}
```



### 简化写法

在实际应用中，我们并不需要使用Lambda表达式完整的语法结构，而是有很多种简化的写法。简写语法有：

- **省略参数名称**：当Lambda表达式的参数列表中只有一个参数时，Kotlin 会隐式为其分配 `it` 名称。因此我们可以省略参数名称和 `->` 符号，从而使 lambda 表达式变得更简洁。
- **将 lambda 表达式直接传入函数**：不需要专门定义一个lambda变量，而是可以直接将lambda表达式当作实参传入函数中。
- **尾随 lambda 语法**：<font color="red">**当 Lambda 参数是函数的最后一个参数时，可以将Lambda表达式移到函数括号的外面**</font>（这个也被称为“尾随 lambda 语法”）。这个规定在 Jecpack Compose 中被广泛使用。



接下来，给出一个逐步简化的例子：

```kotlin
// 写法1
val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon") 
val lambda = { fruit: String -> fruit.length } 
val maxLengthFruit = list.maxBy(lambda) 

// 简化写法2
// 不需要专门定义一个lambda变量，而是可以直接将lambda表达式传入maxBy函数当中
val maxLengthFruit = list.maxBy({ fruit: String -> fruit.length })

// 简化写法3
// Kotlin规定，当Lambda参数是函数的最后一个参数时，可以将Lambda表达式移到函数括号的外面
val maxLengthFruit = list.maxBy() { fruit: String -> fruit.length } 

// 简化写法4
// 如果Lambda参数是函数的唯一一个参数的话，还可以将函数的括号省略
val maxLengthFruit = list.maxBy { fruit: String -> fruit.length } 

// 简化写法5
// 由于Kotlin拥有出色的类型推导机制，Lambda表达式中的参数列表其实在大多数情况下不必声明参数类型
val maxLengthFruit = list.maxBy { fruit -> fruit.length } 

// 简化写法6
// 当Lambda表达式的参数列表中只有一个参数时，也不必声明参数名，而是可以使用it关键字来代替
val maxLengthFruit = list.maxBy { it.length } 
```





### Java 函数式 API 的使用







## 空指针检查

在Android系统上，崩溃率最高的异常类型就是空指针异常 （NullPointerException）。这是因为空指针是一种不受编程语言检查的运行时异常，只能由程序员主动通过逻辑判断来避免，但即使是最出色的程序员，也不可能将所有潜在的空指针异常全部考虑到。

给出一段比较简单的 Java 代码：

```java
// 当study为null时，系统报空指针异常
public void doStudy(Study study) { 
    study.readBooks(); 
    study.doHomework(); 
} 
```

即使是如此简单的一小段代码，都有产生空指针异常的潜在风险。



### 空指针检查机制

Kotlin 却非常科学地解决了这个问题，它**利用编译时判空检查的机制几乎杜绝了空指针异常**。

给出一段比较简单的 Kotlin 代码：

```kotlin
fun doStudy(study: Study) { 
    study.readBooks() 
    study.doHomework() 
} 
```

这段代码看上去和刚才的Java版本并没有什么区别，但实际上它是没有空指针风险的，因为<font color="red"> **Kotlin默认所有的参数和变量都不可为空**</font>，所以这里传入的Study参数也一定不会为空，我们可以放心地调用它的任何函数。如果你尝试向doStudy()函数传入一个null参数，则会提示报错。



### 可空类型系统

如果我们的业务逻辑就是需要某个参数或者变量为空该怎么办呢？不用担心，Kotlin提供了另外一套可为空的类型系统，只不过**在使用可为空的类型系统时，我们需要在编译时期就将所有潜在的空指针异常都处理掉，否则代码将无法编译通过**。

可空类型系统主要是通过 `?`来实现的，**在类名的后面加上一个问号**即可。比如，，`Int` 表示不可为空的整型，而`Int?`就表示可为空的整型；

在修改参数 study 可为空的同时，调用参数的 `readBooks()` 和 `doHomework()` 方法都可能造成空指针异常，因此Kotlin在这种情况下不允许编译通过。为了解决这个问题，添加个判断参数是否空的处理即可。

```kotlin
fun doStudy(study: Study?) { 
    if (study != null) { 
        study.readBooks() 
        study.doHomework() 
    } 
} 
```



### 判空辅助工具

在使用可为空的类型系统时，为了在编译时期就处理掉所有的空指针异常，通常需要编写很多额外的检查代码才行。如果每处检查代码都使用if判断语句，则会让代码变得比较啰嗦，而且if判断语句还处理不了全局变量的判空问题。为此，Kotlin专门提供了一系列的辅助工具，使开发者能够更轻松地进行判空处理。

**`?.`操作符**：当对象不为空时正常调用相应的方法，当对象为空时则什么都不做。

```kotlin
// 判空处理代码
if (a != null) { 
    a.doSomething() 
}

// 等价写法
a?.doSomething()
```

**`?:`操作符**：这个操作符的左右两边都接收一个表达式， 如果左边表达式的结果不为空就返回左边表达式的结果，否则就返回右边表达式的结果。

```kotlin
// 判空处理代码
val c = if (a ! = null) { 
    a 
} else { 
    b 
} 

// 等价写法
val c = a ?: b
```

举个例子，要编写一个函数用来获得一段文本的长度，代码如下：

```kotlin
// 传统写法
fun getTextLength(text: String?): Int { 
    if (text != null) { 
        return text.length 
    } 
    return 0 
} 

fun getTextLength(text: String?) = text?.length ?: 0
```

首先由于text是可能为空的，因此我们在调用它的 length字段时需要使用 `?.` 操作符，而**当text为空时，`text?.length` 会返回一个null值**， 这个时候我们再借助 `?:` 操作符让它返回0。



### let 函数

**let 函数**提供了函数式API的编程接口，并将原始调用对象作为参数传递到 Lambda表达式中。示例代码如下：

```kotlin
obj.let { obj2 -> 
    // 编写具体的业务逻辑 
} 
```

可以看到，这里调用了obj对象的let函数，然后Lambda表达式中的代码就会立即执行，并且 这个obj对象本身还会作为参数传递到Lambda表达式中。不过，为了防止变量重名，这里我将 参数名改成了obj2，但实际上它们是同一个对象，这就是let函数的作用。

let 函数的特性配合 `?.` 操作符 可以在空指针检查的时候起到很大的作用，示例如下：

```kotlin
// 只使用 ?.操作符
// 缺陷：每使用一次 study的方法，都要进行一次判空
fun doStudy(study: Study?) { 
    study?.readBooks() 
    study?.doHomework() 
} 

// 使用let函数和?.操作符
// 优点：无论调用多少次study的方法，只进行一次判空
fun doStudy(study: Study?) { 
    study?.let { 
        it.readBooks() 
        it.doHomework() 
    } 
} 
```

在使用 let 函数和 `?.` 操作符的方法中， `?.` 操作符表示对象为空时什么都不做，对象不为空时就调用let函数，而let函数会将study对象本身作为参数传递到Lambda表达式中，此时的study对象肯定不为空了。

注意，**let函数是可以处理全局变量的判空问题的，而if 判断语句则无法做到这一点**。比如我们将doStudy()函数中的参数变成一个全局变量，使用 let函数仍然可以正常工作，但使用if判断语句则会提示错误



### 空指针检查机制的缺陷

有的时候我们可能**从逻辑上已经将空指针异常处理了，但是Kotlin的编译器并不知道**，这个时候它还是会编译失败。

观察如下的代码示例：

```kotlin
var content: String? = "hello" 
fun main() { 
    if (content != null) { 
        printUpperCase() 
    } 
} 
fun printUpperCase() { 
    val upperCase = content.toUpperCase() 
    println(upperCase) 
} 
```

在这段代码里，main() 函数里进行了一次判空，从逻辑上排除了空指针异常。但是，printUpperCase()函数并不知道外部已经对 content 变量进行了非空检查，在调用 toUpperCase() 方法时，还认为这里存在空指针风险，从而无法编译通过。





## 语法糖

### 字符串内嵌表达式

Kotlin允许我们在字符串里嵌入`${}`这种语法结构的表达式，并在运行时使用表达式执行的结果替代这一部分内容。示例如下：

```kotlin
"hello, ${obj.name}. nice to meet you!" 

// 简化写法，当表达式中仅有一个变量的时候，可以将两边的大括号省略
"hello, $name. nice to meet you!" 
```



### 函数的参数默认值

在定义函数的时候**给任意参数设定一个默认值，这样当调用此函数时就不会强制要求调用方为此参数传值**，在没有传值的情况下会自动使用参数的默认值。示例代码如下：

```kotlin
fun printParams(num: Int, str: String = "hello") { 
    println("num is $num , str is $str") 
} 
fun main() { 
    printParams(123)  // num is 123 , str is hello
}
```

可以看到，这里我们给 printParams() 函数的第二个参数设定了一个默认值，这样当调用 printParams() 函数时，可以选择给第二个参数传值，也可以选择不传，在不传的情况下就会自动使用默认值。

在传统写法中，这个是按照参数定义的顺序来传参。举个错误例子：

```kotlin
fun printParams(num: Int = 100, str: String) { 
    println("num is $num , str is $str") 
}

fun main() { 
    printParams("Tom")  // 报类型不匹配错误
}
```

其中，编译器会认为我们想把字符串赋值给第一个num参数，从而报类型不匹配的错误。

Kotlin 提供了另外一种神奇的机制，就是可以**通过键值对的方式来传参，从而不必像传统写法那样按照参数定义的顺序来传参**。

```kotlin
fun printParams(num: Int = 100, str: String) { 
    println("num is $num , str is $str") 
} 
fun main() { 
    printParams(str = "world") 
} 
```

此时哪个参数在前哪个参数在后都无所谓，Kotlin可以准确地将参数匹配上。而使用这种键值对 的传参方式之后，我们就可以省略num参数了





### 单行代码函数

当一个函数中只有一行代码时，Kotlin 允许我们不必编写函数体，可以直接将唯一的一行代码写在函数定义的尾部，中间用等号连接即可。

```kotlin
// 常规写法
fun largerNumber(num1: Int, num2: Int): Int { 
    return max(num1, num2) 
} 

// 等价写法(使用语法糖)，等号足以表达返回值的意思
fun largerNumber(num1: Int, num2: Int): Int = max(num1, num2) 

// max(num1, num2)返回值类型为Int，类型推导 largerNumber的返回值类型
fun largerNumber(num1: Int, num2: Int) = max(num1, num2) 
```





