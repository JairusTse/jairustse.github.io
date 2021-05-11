---
layout: post
title: kotlin可见性修饰符
category: kotlin
tags: [kotlin,android]
---

可见性修饰符可以对创建的任何代码设置可见性。

kotlin的可见性修饰符有 `public（默认）、private、protected、internal`，可以被修饰的有**类（包括抽象类）、对象、接口、函数（包括构造函数）、属性**。

## 包
`Java`包下面的源文件中的顶级代码只有**类/接口**，但是`kotlin`中，**类/接口、变量、函数**都可以作为顶级代码声明在源文件中。

在 `Intellij IDEA` 中只有**类**的`kotlin`文件和有多种顶级代码的图标上也有区别：

![](https://gitee.com/jairustes/blogimages/raw/master/img/20210511114615.png)

包或者源文件下的代码可以被`public（默认）、private、internal` 修饰：

```
//example.kt
package foo
//源文件或者包中可以定义的顶层声明：类、对象、属性、函数、接口
//包->类
class Example {}
//包->对象
var example: Example = Example()
//包->属性
var name = "Jairus"
//包->函数
internal fun baz() {}
//包->接口
private interface OnClickedListener {}
```

通过包名调用顶级代码:

```
foo.baz()
foo.name
foo.example
```


**修饰符可见性：**  
- `public（可省略）`：所有地方可见
- `private`：同一源文件可见
- `internal`：同一模块可见

## 类/接口
跟 `Java` 一样，`kotlin` 的 `类/接口` 里面可以有**属性、函数**，可以被 `public（默认）、private、protected、internal` 修饰。

**注意事项：**  
- **接口的属性和函数**不能被`internal`修饰
- **局部变量、函数和类**不能被可见性修饰符修饰
- 父类的 `protected` 成员被子类覆盖的话，在子类中默认也是 `protected` 的

```
open class Example {
    //类->属性
    var name = "Jairus"
    //类->函数
    private fun baz() {}
    protected fun baz2() {}
    internal fun baz3() {
        //局部变量
        var name = "Jairus"
        //局部函数
        fun baz() {}
        //局部类
        class Example {}
    }
}

interface OnClickedListener {
    //接口->函数
    fun baz() {}
    private fun baz2() {}
}

```

**修饰符可见性：**  
- `public（可省略）`：类成员在所有地方可见
- `private`：类成员仅在类中可见
- `protected`：类成员仅在类和子类中可见
- `internal`：类成员对同一模块中可以看见类的内容可见


## 模块
一个**模块**是编译在一起的一套`kotlin`文件，如 **Intellij IDEA** 模块。