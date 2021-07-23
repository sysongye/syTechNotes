# <font color=#69D600>Maven Command</font>

[TOC]

平台：Windows
```perl
# 该命令打印出所有的java系统属性和环境变量。
mvn help:system

# 清理输出目录默认target/。
mvn clean

# 编译项目主代码，默认编译至target/classes目录下。
mvn clean compline

# maven测试，但实际执行的命令有：clean:clean，resource:resources，compiler:compile, resources:testResources,compiler:testCompile，
# maven在执行test之前，会先自动执行项目主资源处理，主代码编译，测试资源处理，测试代码编译等工作，
# 测试代码编译通过之后默认在target/test-calsses目录下生成二进制文件，
# 紧接着surefile:test 任务运行测试，并输出测试报告，显示一共运行了多少次测试，失败成功等等。
mvn clean test

# maven打包，maven会在打包之前默认执行编译，测试等操作，打包成功之后默认输出在target/目录中。
mvn celan package

# maven安装，让其他的项目直接引用这个项目。
mvn clean install

# 查看maven安装路径。
echo %MAVEN_HOME%

# 检查是否安装了maven。
mvn

# 查看当前项目中的已解析依赖
mvn dependency:list

# 查看当前项目的依赖树
mvn dependency:tree

# 查看当前项目中使用未声明的依赖和已声明但未使用的依赖
mvn dependency:analyze

```







### DONE



