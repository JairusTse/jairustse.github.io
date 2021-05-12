---
layout: post
title: kotlin嵌套类和内部类
category: kotlin
tags: [kotlin,android]
---

**嵌套类**是定义在另一个类中的类。

如果希望为**外部类**提供超出其主要目的的额外功能，或者使代码更接近于其使用位置，可以使用嵌套类（**类和接口可以相互嵌套**）。

嵌套类不能访问外部类成员，**内部类**是可以访问外部类成员的嵌套类。

```
class Outter {
    val x = "这是外部类"

    class Nested {
        val y = "这是嵌套类"
        fun show() {
            println("嵌套类的show函数")
        }
    }
    //嵌套接口
    interface InnerInterface
}
```

调用嵌套类：

```
val nested = Outter.Nested()
nested.y
nested.show()
```

嵌套类前面加上 `inner` 使其成为内部类：

```
class Outter {
    val x = "这是外部类"

    inner class Inner {
        val y = "这是内部类"
        fun show() {
            println("内部类调用外部类成员：$x")
        }
    }
    //嵌套接口
    interface InnerInterface
}
```

内部类持有一个外部类对象的引用，所以使用内部类时要调用外部类的**构造函数**，创建外部类的对象：

```
val inner = Outter().Inner()
inner.y
inner.show()
```

