---
layout: post
title: kotlin扩展
category: kotlin
tags: [kotlin,android]
---

**扩展**可以向现有类型添加新函数和属性，而不需要创建全新的子类型。


## 扩展函数
通过 `fun 接受者类型.扩展函数名(参数..){函数体..}` 的方式给**接受者类型**添加扩展函数：
```
fun String.showHaha() {
    println("哈哈")
}

class Duck{
    fun getDuck() {
        "duck".showHaha()
    }
}
```

**扩展并没有在类中插入新成员，而是通过该类的变量用点表达式去调用这个新函数。**


## 扩展属性

通过 `var/val 接受者类型.扩展属性名: 扩展属性类型` 的方式给类添加扩展函数。

**注意事项：**  
- 变量扩展属性用 `var` 修饰，常量扩展属性用 `val` 修饰
- `var` 修饰的扩展属性必须实现`get/set`方法，`val` 修饰的扩展属性必须实现`getter`
- 扩展属性没有幕后字段，不能定义初始化器（即不能直接赋值，必须自己实现`get/set`方法）
- **扩展属性不能保存值，其属性访问器只能调用对象中的属性和方法，不能调用扩展属性本身**


```
val Duck.count: Int
    get() = this.age


var Duck.newAge: Int
    get() = this.age
    set(value) {
        this.age = value
    }

class Duck{
    var age = 10
    var name = "myDuck"
}
```

## 扩展的作用域
**扩展**的可见性与相同作用域内声明的其他实体的可见性相同。大多数时候我们在包的顶层定义扩展。要使用其他包中定义的扩展，需要**导入**。