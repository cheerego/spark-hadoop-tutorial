### 配置与搭建
1. 安装JDK并配置环境变量(Scala的运行依赖JVM，版本需要1.8因为需要解析Lamada表达式)
2. Scala官网下载binary压缩包，并配置环境变量(版本一定要与服务器上配置的版本一样)
3. 下载IntelliJ Idea(社区版免费)
### Idea Scala插件安装
安装Idea完成后，进入软件点击`File->Settings->Plugins->Browse repositories`,在输入框中搜索`scala`,然后点击Install。然后新建项目的选项中就会有了scala，在新建的过程中可能会需要设置scala路径和jdk路径。
### Scala程序的构建
scala程序构建有两种方式：
1. maven
2. sbt(Simple build tool)

### 使用Maven
在idea中点击`File->Project Structure(或者点击右上角的Project Structure图标)->Libraries->+->From Maven`,然后在输入框中输入spark，找到spark-core的jar包。到这里idea会自动帮你下载相关依赖构建项目。

### 使用一个简单方法(推荐)

![image](./)
之前下载过的spark-2.0.1-bin-hadoop2.7.tgz,将里面的jars中所有的jar文件拷贝到工程目录下的lib目录下。
在idea中点击`File->Project Structure(或者点击右上角的Project Structure图标)->Libraries->+->Java  选择lib目录`
### 使用SBT
。。。。。。。。

在使用中就可以发现可以使用Spark相关的class了。


