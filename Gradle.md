## Groovy   
gradle基于groovy;     

1. 基本方法参数定义  

	    // Groovy中支持动态类型，即定义变量的时候可以不指定其类型。Groovy中，变量定义可以使用关键字def。注意, def不是必须的
	    def variable1 = 1   //可以不使用分号结尾  
	    def varable2 = "I ama person" 
	    def  int x = 1  //变量定义时，也可以直接指定类型    
	
	    //变量定义：List变量由[]定义，比如  
	    def aList = [5,'string',true] //List由[]定义，其元素可以是任何对象 
	
	    //变量定义：Map变量由[:]定义，比如  
	    def aMap = ['key1':'value1','key2':true]  
	
	    //Range类型的变量 由begin值+两个点+end值表示,下面这个aRange包含1,2,3,4,5这5个值
	    def aRange = 1..5   
	    //如果不想包含最后一个元素，则  
	    def aRangeWithoutEnd = 1..<5  <==包含1,2,3,4这4个元素  

2. 字符串

    1. 单引号''中的内容严格对应Java中的String，不对$符号进行转义  

        	def singleQuote='I am $ dolloar'  //输出就是I am $ dolloar 

    2. 双引号""的内容则和脚本语言的处理有点像，如果字符中有$号的话，则它会$表达式先求值。
    3. 三个引号'''xxx'''中的字符串支持随意换行

3. 函数 
	
	    //函数定义时，参数的类型也可以不指定。比如  
	    String testFunction(arg1,arg2){//无需指定参数类型  
	      ...  
	    } 
	
	    //无类型的函数定义，必须使用def关键字  
	    def  nonReturnTypeFunc(){  
	        last_line   //最后一行代码的执行结果就是本函数的返回值  
	    } 
	
	    //如果指定了函数返回类型，则可不必加def关键字来定义函数  
	    String getString(){  
	       return"I am a string"  
	    }  

2. 闭包 Closure
    一段单独的代码块; 

	    //闭包是一段代码，所以需要用花括号括起来..
	    def aClosure = {
	        String param1, int param2 ->  //这个箭头很关键。箭头前面是参数定义，箭头后面是代码  
	        println"this is code" //这是代码，最后一句是返回值，  
	       //也可以使用return，和Groovy中普通函数一样  
	    }
	
	    // 闭包执行,  闭包对象.call(参数)   或者  闭包对象(参数) 
	    aClosure.call("this is string",100)  
	    aClosure("this is string", 100) 
	
	    //如果闭包没定义参数的话，则隐含有一个参数，这个参数名字叫it，和this的作用类似。it代表闭包的参数。
	    def greeting = { "Hello, $it!" }  
	    // 等同于：  
	    def greeting = { it -> "Hello, $it!"} 


  - closure作为参数传递
    1. 只接收一个参数，且参数是closure的方法： myMethod(myClosure) 
    2. 如果方法只接收一个参数，括号可以省略： myMethod myClosure 
    3. 可以使用内联的closure： myMethod {println ‘Hello World’} 
    4. 接收两个参数的方法： myMethod(arg1, myClosure) 
    5. 和4类似，单数closure是内联的： myMethod(arg1, { println ‘Hello World’ }) 
    6. 如果最后一个参数是closure，它可以从小括号从拿出来： myMethod(arg1) { println ‘Hello World’ }

> 每行代码可以省略分号
> Groovy中函数调用的时候, 如果方法只接收一个参数，括号可以省略

## Gradle  

### 基本概念
  1. gradle中是按project来构建项目
  2. 每一个Project都必须设置一个build.gradle   
  3. 通常我们会把多个项目现在一个根文件夹下,组成一个Multi-project; 对于Multi-projec项目, 要在根目录放一个build.gradle和一个settings.gradle, setting.gradle通过`include`配置,引入各个子项目
  4. 一个Project是由若干个tasks组成, 执行gradle xxx命令, 实际是要gradle执行xxx任务
  5. 一个Project包含哪些Task, 是由build.gradle中指定的插件控制; 插件是什么呢？插件就是用来定义Task，并具体执行这些Task的东西


### Gradle基本命令

1. gradle projects 查看工程的project   
  ![gradle_project_out](http://obh9ec69s.bkt.clouddn.com/682349ed0c87e1cc28c7551f55503084.png)  

2. gradle tasks 查看任务信息
![gradle_tasks_out](http://obh9ec69s.bkt.clouddn.com/023d90e371859a780b685e798e39dd2b.png)    
  上面只是列出了所有task, 加上--all,可以显示task之间的依赖关系; `gradle tasks --all`

3. gradle task-name 执行任务<br>
    上面通过`gradle tasks`列出所有任务名, 然后就可以通过 gradle task-name 执行某个任务    
    对于子项目的任务, 可以通过 gradle project-path:task-name 执行, project-path是子项目的文件夹; eg: gradle app:clean
4. gradle dependencies 查看项目的依赖关系    
![gradle_dependencies_out](http://obh9ec69s.bkt.clouddn.com/0406c1bb2f2ed35b1dd07cbac5adcb57.png)  
  上面会显示所有阶段的依赖, 如果只想看指定阶段的依赖,可以使用 --configuration 来指定   
  `gradle app:dependencies --configuration testCompile`


### 工作流程
  具体可参考https://docs.gradle.org/current/userguide/build_lifecycle.html
  1. Initialization, 初始化阶段    
      gradle支持单项目和 multi-project, 在这个流程中,gradle会判断是哪种项目类型,并且为每个项目生成'Project'实例;  一般我们的项目来说, 就是执行settings.gradle
  2. Configuration   
      解析每个项目的buil.gradle, 确定项目的配置和各个task,及task之间的依赖关系
  3. Execution   
      这个没啥说的, 根据传进来的task-name 执行
