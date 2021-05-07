---
layout: post
title: 数据类Data Class
category: kotlin
tags: [kotlin,android]
---

kotlin 数据类 Data Class

#### 概述
`kotlin` 的数据类 `Data Class`，是只用来保存数据的类，作用类似 `Java Bean` 和 `Android` 的 `Model`，自动帮我们实现了属性的`get、set` 方法和 `toString()` 方法，很方便。


#### Data Class 自带的方法
除了上面说的`get、set` 和 `toString()`，数据类还会自动实现以下函数：
- `equals()/hashCode()`  
- `componentN()`函数
- `copy()`函数


##### `equals()/hashCode()`
`Data Class` 重写了父类 `Any` 的 `equals()/hashCode()` 方法。非数据类的 `equals()` 方法是根据对象的**引用**是否相同来判断两个对象是否相等。

下面的代码中，cat1 和 cat2 会被认为不相等：
```
class Cat(val name:String, val color:String, val age:Int)

var cat1 = Cat("小白", "white", 10)
var cat2 = Cat("小白", "white", 10)

cat1.equals(cat2) //false，因为cat1和cat2的引用不同
```

但是在数据类中，我们更想要的是根据对象的**属性值**来判断两个对象是否相等。所以重写了 `equals()` 函数。

下面的代码中，cat1 和 cat2 会被认为相等：
```
data class Cat(val name:String, val color:String, val age:Int)

var cat1 = Cat("小白", "white", 10)
var cat2 = Cat("小白", "white", 10)

cat1.equals(cat2) //true，因为Cat是数据类，cat1和cat2属性值相等
```

两个相等的对象，它们的Hash值也要相等，所以数据类的 `hashCode()` 函数也被重写了，两个属性值相等的对象，`hashCode()` 也相等。

**注意：**

- `==` 操作符调用的是 `equals()` 函数，结果也跟 `equals()` 一致；
- 如果要比较数据类的引用，要用 `===` 操作符；
- `equals()` 只会比较主构造函数里面的属性，不比较类体中声明的属性。
    
    
下面的代码中，cat1 和 cat2 会被认为相等：
```
data class Cat(val name:String, val color:String, val age:Int) {
    var weight:Int = 0
}

var cat1 = Cat("小白", "white", 10)
cat1.weight = 5
var cat2 = Cat("小白", "white", 10)
cat1.weight = 6

cat1.equals(cat2) //true，因为cat1和cat2的主构造函数值相等

```
  

##### `componentN()`
数据类可以通过 `componentN()` 解构成多个变量（**仅主构造函数里面的属性**）。

下面代码中，获取数据类的属性：
```
var cat1 = Cat("小白", "white", 10)

//获取Data Class的全部属性
var name = cat1.name
var color = cat1.color
var age = cat1.age

//相当于
var name = cat1.component1()
var color = cat1.component2()
var age = cat1.component3()

//相当于
val (name, color, age) = cat1

println("name=$name, color=$color, age=$age")
```

通过 `componentN()` 依次获取数据类主构造函数的属性，最多到 `component20()`，如果中间某些属性不需要，可用**下划线** ' `_`' 占位。

```
val (name, _, age) = cat1
```


使用数据类的 `componentN()` 特性，可以实现函数返回多个值。

下面代码中，把数据类作为函数返回值，利用数据类的解构特性赋值给多个变量：

```
fun returnMultiValues(): Cat {
    return Cat("小黄猫", "yellow", 15)
}

val (name, color, age) = returnMultiValues()
println("name=$name, color=$color, age=$age")
```


##### `copy()`
可以把数据类**复制**到另一个对象，同时改变一些属性。


下面代码中，cat3从cat1复制而来，属性值相同，但是引用不同：
```
data class Cat(val name:String, val color:String, val age:Int) 

var cat1 = Cat("小白", "white", 10)
var cat2 = cat1.copy(name = "小黑", color = "black")
var cat3 = cat1.copy()

println(cat1 == cat3) //true，属性值一样
println(cat1 === cat3) //false，引用不一样
```

##### 深拷贝和浅拷贝

**浅拷贝：** 新对象会拷贝就对象的属性值，如果属性是基本类型，就会拷贝基本类型的值；如果是引用，就会拷贝引用（其中一个对象修改了引用，会影响另一个对象）。

**深拷贝：** 新对象会拷贝所有属性并重新分配内存，互不影响（开销更大）。

`copy()` 函数是 **浅拷贝**。

下面代码中，拷贝的对象和旧对象相互影响：
```
data class Cat(var defaultName:DefaultName, var color:String, var age:Int)
data class DefaultName(var name:String)

var defaultName = DefaultName("小白")
var cat1 = Cat(defaultName, "white", 10)
var cat2 = cat1.copy()

cat2.defaultName.name = "小黑"

println(cat1.defaultName.name) //受cat2影响，变成小黑
println(cat2.defaultName.name) //小黑
println(defaultName.name) //受cat2影响，变成小黑

```


#### 需要注意的地方
- `Data Class` 是一个 `final` 类 **（不能被继承，`allopen`插件可破）**
- 主构造函数至少有一个参数 **（如果要无参的构造函数，就给所有属性指定默认值）**
- 主构造函数中的参数都要声明`var`或者`val` **（最好是`val`）**
- `componentN()` 和 `copy()` 无法被显式实现 **（不能重写这两个方法）**
- **大多数情况**不需要函数体
- 属性类型**最好**为基本类型、`String`、其他`Data Class`


**`Data Class` 的本质：**
```
data class Person(val name: String, val age: Int = 18)
    public final data class Person public constructor(name: kotlin.String, age: kotlin.Int) {
        public final val age: kotlin.Int
        public final val name: kotlin.String
        public final operator fun component1(): kotlin.String
        public final operator fun component2(): kotlin.Int
        public final fun copy(name: kotlin.String, age: kotlin.Int): Person
        public final fun toString(): String
        public final fun equals(other: Any?): Boolean
        public final fun hashCode(): Int
    }
```

#### Android中使用

我自己主要在 `Android` 中使用，记录几个常见用法：

- 原来实体类都会继承 `BaseResult`，因为`Data Class`不能被继承，改成**接口**实现；
- 多层嵌套的对象写在同一个文件中；
- 支持 `GSON` 的 `@SerializedName` 注解。  


`IBaseResult.kt` 类：
```
package com.jt.base.model.base

/**
 * Http Result基类接口
 * @author xieyue
 * @time 2021/5/6 15:55
 */
interface IBaseResult {
    fun getErrorMsg():String
    fun getErrorCode(): Int
}
```

`WeatherData.kt` 类：
```
package com.baseproject.model

import com.google.gson.annotations.SerializedName
import com.jt.base.model.base.IBaseResult

/**
 *
 * @author xieyue
 * @time 2021/5/7 16:10
 */
data class WeatherData(@SerializedName("reason") var error_msg: String, var error_code: Int,  val result:Result): IBaseResult {

    override fun getErrorMsg(): String {
       return error_msg
    }

    override fun getErrorCode(): Int {
        return error_code
    }
}

data class Result(val city:String? = null, val realtime:Realtime? = null, val future:List<Future>? = null)

data class Future(val date:String? = null, val temperature:String? = null, val weather:String? = null,
                  val wid:Wid? = null, val direct:String? = null)

data class Wid(val day:String? = null, val night:String? = null)

data class Realtime(val temperature:String? = null, val humidity:String? = null,
                    val info:String? = null, val wid:String? = null, val direct:String? = null,
                    val power:String? = null, val aqi:String? = null)
```

完毕。