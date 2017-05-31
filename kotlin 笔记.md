
1. 可变对象 var

        var x = 1
        var str : String = ""

1. 不可变对象 val

        val x = 1

> kotlin没有final关键字, 也没有静态变量

1. 强制类型转换 as
        var str = "hello" as String     // 类型转换可能抛异常
        var str = "hello" as String?    // 安全的类型转换

1. 类型判断 is  

    var str = "hello"
    if (str is String){

    }

1. 匿名类

        Object : 

        val mHandler = object : Handler() {
            override fun handleMessage(msg: Message?) {
                super.handleMessage(msg)
            }
        }