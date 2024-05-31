# kotlin密封类

当一个值为有限几种的类型、而不能有任何其他类型时，可以使用密封类。密封类是枚举类的扩展，比枚举类更加灵活。

## 密封类比枚举类灵活的地方：
- 每个枚举常量只有一个实例（改动会影响到其他使用到的地方），而密封类的一个子类可以有多个实例。
- 每个枚举常量必须有相同的属性和函数，而密封类的每个子类都可以定义自己的属性和函数。

## 定义密封类：
```
//密封类
sealed class MessageType
//密封类的两个子类
class MessageSuccess(var msg:String): MessageType()
class MessageFailuer(var msg:String, var e:Exception): MessageType()
```

## 使用密封类（常配合 `when` 表达式使用）：
```
val messageSuccess1 = MessageSuccess("请求成功")
val messageSuccess2 = MessageSuccess("回调成功")
val messageFailuer = MessageFailuer("请求失败", Exception("请求失败"))

var myMessageType: MessageType = messageFailuer
//配合when表达式使用
val myMessage = when(myMessageType) {
    is MessageSuccess-> myMessageType.msg
    is MessageFailuer-> myMessageType.msg + " " + myMessageType.e.message
}
```