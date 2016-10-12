## Groovy   
gradle基于groovy;     
1. 基本方法参数定义  
2. 闭包


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
