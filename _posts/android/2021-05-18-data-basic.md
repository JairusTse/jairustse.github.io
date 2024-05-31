---
layout: post
title: kotlin基本类型
category: kotlin
tags: [kotlin,android]
---

记录kotlin基本类型的特别之处。

## kotlin基本类型

kotlin的基本类型包括以下几种：   

- 数字：`Byte`、`Short`、`Int`、`Long`、`Float`、`Double`
- 布尔：`true`、`false`
- 字符：`'A'` `'B'` `'C'`
- 字符串：`"kotlin"`
- 数组：`arrayOf(1, 2, 3)`  

## 装箱
在Java平台下，数字是JVM原生类型（直接存放在内存栈中，不存在"引用"的概念），但是如果是一个可空类型或泛型，会对数字进行装箱（封装成对象）。

```
var a = 128 //基本数据类型，存放在内存栈中，不存在"引用"的概念

var a1: Int? = a //可空类型，会进行装箱
var a2: Int? = a
println(a1 === a2) //false，对象引用不同
```

**注意：**  
如果 `a` 的范围在 **[-128, 127]** 之间，上面代码中 `a1 === a2` 结果是 `true`。

查看上面代码的 `Java` 字节码：
```
int a = 128;
Integer a1 = Integer.valueOf(a);
Integer a2 = Integer.valueOf(a);
boolean var3 = a1 == a2;
boolean var4 = false;
System.out.println(var3);
```

查看 `Integer.valueOf()` 的源码：
```

/**
 * Returns an {@code Integer} instance representing the specified
 * {@code int} value.  If a new {@code Integer} instance is not
 * required, this method should generally be used in preference to
 * the constructor {@link #Integer(int)}, as this method is likely
 * to yield significantly better space and time performance by
 * caching frequently requested values.
 *
 * This method will always cache values in the range -128 to 127,
 * inclusive, and may cache other values outside of this range.
 *
 * @param  i an {@code int} value.
 * @return an {@code Integer} instance representing {@code i}.
 * @since  1.5
 */
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

**注释翻译：**  
>返回一个表示指定int值的Integer实例。如果不需要一个新的Integer实例，则通常应该优先使用此方法，而不是构造函数Integer(int)，因为该方法通过缓存频繁请求的值，可能会显著提高空间和时间性能。此方法将始终缓存范围为-128到127(包括在内)的值，并可能缓存此范围之外的其他值。


为了提高性能，`a` 的值在 **-128** 到 **127** 之间时，从缓存中拿，不会创建新的 `Integer` 对象。

## 类型转换
**所有数字类型都支持以下的转换：**

`toByte(): Byte`  
`toShort(): Short`  
`toInt(): Int`  
`toLong(): Long`  
`toFloat(): Float`  
`toDouble(): Double`  

**注意：**  
从存储小数据的类型转换成大数据的类型没问题，但是从大数据转成小数据要小心溢出（比如 `999.toByte()` 会得到意想不到的结果），所有类型转换要保证不会溢出。


## 数字类型

- **可以使用下划线增强可读性：**

```
val a = 1234_5678_9012_3456 //相当于a1
val a1 = 1234567890123456
val b = 1234_5678_9012_3456L  //相当于b1
val b1 = 1234567890123456L
```



## 字符串

### 字符串模板
`String` 里面可以引用变量（`$变量名`）和调用函数（`${函数}`）：

```
var str = "我的名字是 $name , 今年${user.age}岁"
```


### 转义 
除了支持反斜杠(`\n`)转义外， 使用三个引号（`"""`）对原始字符串进行输出（没有转义并且可以包含任何字符）：

```
val text = """
for (c in "foo")
    print(c)
"""
```
可以通过 `trimMargin()` 函数去除空格：

```
    val text = """
        |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

## 数组
### 原生类型数组（无装箱）

默认数组（`arrayof()`）是支持可空类型的（有装箱），如果明确没有空类型，可以用原生类型数组 `ByteArray、ShortArray、IntArray` 节省资源开销。

`arrayOf(int, int)` 默认是 `Integer[]`，而原生类型数组是 `int[]`：

```
var arr1: Array<Int?> = arrayOf(100, null, 300, 400, 500)
var arr2 = arrayOf(100, 200, 300, 400, 500)

var arr3 = IntArray(2)
arr3[0] = 0
arr3[1] = 1
```

`Java` 字节码：
```
Integer[] var10000 = new Integer[]{100, (Integer)null, 300, 400, 500};
var10000 = new Integer[]{100, 200, 300, 400, 500};
int[] arr3 = new int[]{0, 1};
```
