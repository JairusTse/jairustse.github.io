---
layout: post
title: kotlin枚举类
category: kotlin
tags: [kotlin,android]
---

枚举类是创建一组值，选其中一个作为变量的唯一有效值。

如果需要限制变量的值从几个特定的值中选其中一个，就可以用枚举类。

一个简单的枚举类：
```
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

枚举类中的选项称为**枚举常量**，上面例子中的 `NORTH, SOUTH, WEST, EAST` 就是枚举常量。每一个枚举常量都是一个对象（例子中都是 `Direction` 的对象）。


枚举类可以有构造函数，每个枚举常量要调用构造函数初始化对象：

```
enum class Direction(val introduce:String) {
    NORTH("北"), 
    SOUTH("南"), 
    WEST("西"), 
    EAST("东")
}

//使用
var selectDirection: Direction
selectDirection = Direction.EAST
selectDirection.introduce
```

枚举类中可以有属性和函数：

注意事项：  
- 枚举常量和属性、函数之间要用 `;` 隔开；
- 函数可以被覆盖
- 枚举常量自带获取名称和位置的属性，实现了 `Comparable` ，可以按位置进行排序。

```
enum class Direction(val introduce:String) {
    NORTH("北") {
       override fun sings() = "北方happy"
    },
    SOUTH("南") {
        override fun sings() = "南方happy"
    },
    WEST("西"),
    EAST("东");

    open fun sings() = "happy"
}

var selectDirection: Direction = Direction.EAST
println(selectDirection.sings())
println(selectDirection.name) //返回名称：EAST
println(selectDirection.ordinal) //返回位置：3

```