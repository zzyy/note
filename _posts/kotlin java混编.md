Kotlin Java 混编
--------

## 1. Class 调用

1. Android中, 很多地方需要穿件Java的Class, 在Kotlin中Java类的Class对象为 `XXX::class.java`

        var i = Intent(context, ExampleActivity::class.java)


##　2. Kotlin 关键字冲突

很多在Java中不是关键字的，　在Kotlin中是关键字，eg. `is`, `in`, 从而不能作为Kotlin中的方法变量名； 可能某些原先某些接口已经定义好， 并且和Kotlin关键字冲突，可以在写Kotlin是， 对关键字加上反引号 ` ；

        fun `is`(){
            var `is` = ""
        }


## 3. static 方法

- Kotlin中是没有static关键字的；
- Kotlin中静态常量， 是使用`const val`；
- Kotlin中的静态方法       
    1.  可以在方法上加上 `@JvmStatic`注解， 但是这个注解， 需要对象是object声明的， 实际上是一个饿汉式的单例；      
    2.  使用伴生对象`companion object`, 但是在Java中调用，需要`XXX.Companion.method()`, Kotlin中调用可以省略Companion， 直接调用`XXX.method()`;
