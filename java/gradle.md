<font size=8>gradle学习笔记</font>

## 1、什么是gradle

***简介：***Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化构建工具。它使用一种基于Groovy的特定领域语言来声明项目设置，而不是传统的XML。Gradle就是工程的管理，帮助我们做了依赖、打包、部署、发布、各种渠道的差异管理等工作。如果没有自动化构建工具，我们需要先执行javac命令先将java源码编译成class文件，然后再执行jar命令再把class文件打成jar包

***发展历程：***Ant  >> Maven >> Gradle

- Ant：Ant的功能虽然强大，但是过于灵活，规范性不足，对目录结构及build.xml没有默认约定，且没有统一的项目依赖管理
- Maven：Maven解决了规范性的问题，也顺带解决了依赖统一管理的问题，但由于规范性太强，灵活性不足，pom.xml采用xml结构，随着项目体量的增加，XML文件就变得冗长。
- Gradle：综合了Ant和Maven的优点，吸收了Ant中task的思想，然后把Maven的目录规范以及仓库思想也融合了进来，但允许用户自由的修改默认的规范（如：可随意修改源码目录），配置文件则采用Groovy语言书写，Groovy是一门可编程语言，配置文件就可以视为一份源代码，并最终交由Gradle处理执行

其实Gradle跟Ant、Maven等构建工具完成工作差不多，但Gradle更加强大，它不仅仅是一个构建工具，还是一个开发框架。

## 2、关于使用Gradle管理项目的两个基本概念

### a、项目（projects）

指我们的构建产物（比如构建jar包，构建一个web应用）一个项目包含一个或多个任务

### b、任务

指不可分的最小工作单元，每个任务代表构建过程当中的一个原子性操作。执行构建工作（比如编译项目或执行测试，打包）

任何一个Gradle构建都是由一个或者是多个projects组成，而一个projects又是由多个task组成，每个project或许是一个jar包或者是一个web应用，也可以是一个由许多其他项目中产生的jar构成的zip压缩包

## 3、构建Gradle基础

编程的灵魂——hello word!

```groovy
task hello{
    doLast{
        println 'hello world!'
    }
}
```

上述脚本定义了一个叫hello的task，并且给它添加一个动作，在调用这个gradle文件的时候就会调用hello任务来执行给定操作。

















































































