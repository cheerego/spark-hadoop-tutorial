# 安装Spark教程
### 教程是安装两台主机进行配置
#### 安装步骤
* 下载相关软件
* 配置环境变量
* 配置免密码登陆的ssh
* 配置hadoop
* 配置spark

#### 下载相关软件
1. jdk *多少位系统下载多少位版本*
2. hadoop *下载二进制包，切记不要下载源码包*
3. spark *同上*
4. scala *同上*
5. 解压命令 tar -zxvf 进行解压
6. 使用mv命令将其全部移动到/usr/local目录下

#### 配置环境变量
```bash
cd ~
vim .bashrc
在末尾处添加
JAVA_HOME=/usr/local/jdk1.8.1
JRE_HOME=$JAVA_HOME/jre
CLASS_PATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
HADOOP_HOME=/usr/local/hadoop-2.7.3
SCALA_HOME=/usr/local/scala-2.11.8
SPARK_HOME=/usr/local/spark-2.0.1
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$SCALA_HOME/bin
source .bashrc
```
# 配置ssh
##### 1. 测试是否安装了openssh-server
```bash
测试电脑是否有ssh
如果有使用ssh localhost测试是否可以登录
如果可以登录，说明本机已经安装了openssh-server
如果无法登录 在Ubuntu下使用sudo apt-get install openssh-server
再次使用 ssh localhost
如果服务无法启动 /etc/init.d/ssh start
```
##### 2. 配置免秘钥登录
* 将自己添加到免密码登录
```bash
生成公钥和私钥，authorized_keys适用于保存所有允许以当前用户身份登录到ssh客户端用户的公钥内容
说明:authorized_keys 文件可能是不存在的，下面的命令是存在则追加，不存在这创建
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa 
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
设置完成后，用下面的命令测试是否需要密码
ssh localhost
上面的作用就是将自己添加的到免密码登录，master和worker都需要配置
```
* 将worker的公钥传到master中
```bash
在worker中配置
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa 
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
测试是否配置成功
ssh localhost 
传输id_rsa.pub 到master中
scp ~/.ssh/id_rsa.pub root@Master:/root/.ssh/id_rsa.pub.worker  (Master代替的是ip地址)
如果有多台worker，分别在两个worker中执行
scp ~/.ssh/id_rsa.pub root@Master:/root/.ssh/id_rsa.pub.worker01
scp ~/.ssh/id_rsa.pub root@Master:/root/.ssh/id_rsa.pub.worker02
```
* master将worker传过来的公钥添加到authorized_keys中
```bash
回到master中执行
cat ~/.ssh/ip_rsa.pub.worker >> ~/.ssh/authorized_keys
如果是多个worker
cat ~/.ssh/ip_rsa.pub.worker01 >> ~/.ssh/authorized_keys
cat ~/.ssh/ip_rsa.pub.worker02 >> ~/.ssh/authorized_keys
以上不步骤就是使worker可以免密码登陆master
在worker(192.168.1.7)中使用下面命令测试是否可以免密码登陆master(192.168.1.6)
ssh root@192.168.1.6
```
* master将自己的authorized_keys传到worker中，以实现master登陆worker的免密码
```bash
在master中
scp ~/.ssh/authorized_keys root@192.168.1.7:/root/.ssh/authorized_keys
如果有多台
scp ~/.ssh/authorized_keys root@192.168.1.7:/root/.ssh/authorized_keys
scp ~/.ssh/authorized_keys root@192.168.1.8:/root/.ssh/authorized_keys
master中使用下面命令测试是否配置成功
ssh root@192.168.1.7
```
# 配置Hadoop
**本教程使用了两台主机，Hadoop需要配置在这两台主机上，这里先将Hadoop配置master中，另外一台的安装方法是一样的**
* 配置 /etc/hosts  先配置master再配置worker
```bash
vim /etc/hosts
在尾部添加
192.169.1.6 master
192.169.1.7 worker01
```
* 配置 /etc/hostname  先配置master再配置worker
```bash
vim /etc/hostname
有就修改没有就添加
master中为master
worker中为worker01
```
* 创建目录
```bash
cd /usr/local/hadoop-2.7.3
mkdir tmp
mkdir dfs
cd dfs 
mkdir name
mkdir data 
```
* 编辑core-site.xml
```xml
cd /usr/local/hadoop-2.7.3/etc/hadoop
vim core-site.xml
在configuration中添加
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://127.0.0.1:9000</value>
    </property>
     <property>
        <name>hadoop.tmp.dir</name>
        <value>/usr/local/hadoop-2.7.3/tmp</value>
    </property>
</configuration>
```
* 编辑mapred-site.xml
```xml
cp mapred-site.xml.template mapred-site.xml
vim mapred-site.xml
在configuration中添加
<configuration>
    <property>
        <name>mapred.job.tracker</name>
        <value>127.0.0.1:9001</value>
    </property>
</configuration>
```
* 编辑hdfs-site.xml
```xml
vim hdfs-site.xml
在configuration中添加
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/usr/local/hadoop-2.7.3/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/usr/local/hadoop-2.7.3/dfs/data</value>
    </property>
</configuration>
```
* 修改masters和slaves文件内容
```bash
masters存在则修改，不存在则创建
cd /usr/local/hadoop-2.7.3/etc/hadoop
echo master > masters
vim slaves
在文本中添加worker01
```
* 配置worker的hadoop
```bash
将必要的软件和.bashrc中的内容和master中配置好的hadoop文件夹复制到worker中
在此教程中有：
/usr/local/hadoop-2.7.3 (这个配置好的
hadoop文件夹)
jdk
scala
.bashrc 中添加的内容
Warning：位置和目录名一定要匹配,配置完成一定要测试一下
如果是U盘copy过去的可能是没有执行的权限的
chmod -R 755 /usr/local/hadoop-2.7.3/bin
chmod -R 755 /usr/local/hadoop-2.7.3/sbin/*
java -version
hadoop version 
等等是否可以成功运行
```
* 在master中格式化集群文件系统
```bash
hadoop namenode -format
可以看到没有任何的错误，就是成功的
```
* 在master中启动hadoop集群服务
```bash
cd /usr/local/hadoop-2.7.3/sbin
./start-all.sh （与先start-dfs.sh 和 再start-yarn.sh是等价的，所有stop-all.sh是一样的）
在这个期间可以需要输入几次yes
然后发现没有报错就是成功的
```
* 使用jps命令查看服务的运行情况
```bash
master：
    27266 NameNode 
    27459 SecondaryNameNode
    27619 ResourceManager
    27879 Jps
worker:
    5425 Jps
    4902 NodeManager
    4748 DataNode
简要的说明各个服务的左右
HDFS: hadoop distribution file system 分布式文件系统
NameNode:名称节点，这个节点存储在master上，可以理解为NameNode是书签
SecondaryNameNode：是NameNode的备份
ResourceManager:资源管理器
DataNode:数据节点，是NameNode对应的数据
replication:是DataNode的备份(为什么没有不知道)
显示效果是和Spark核心源码分析与开发实战的显示效果是匹配的（但是强烈觉得这本书不怎么样）
```
# 配置Spark
**本教程使用了两台主机，Spark需要配置在这两台主机上，这里先将Spark配置master中，另外一台的安装方法是一样的**
* 配置spark-env.sh
```bash
cd /usr/local/spark-2.0.1/conf 
cp spark-env.sh.template spark-env.sh
vim spark-env.sh
在末尾处添加
export JAVA_HOME=/usr/local/jdk1.8.1
export SCALA_HOME=/usr/local/scala-2.11.8
export HADOOP_HOME=/usr/local/hadoop-2.7.3
export HADOOP_CONF_DIR=/usr/local/hadoop-2.7.3/etc/hadoop
export SPARK_MASTER_IP=192.168.1.6
export SPARK_WORKER_MEMORY=2g
export SPARK_WORKER_CORES=2
export SPARK_WORKER_INSTANCES=1
注意:
SPARK_MASTER_IP:指的是spark集群master节点的ip地址
SPARK_WORKER_MEMORY:指的是每个worker节点最大能够分配给Exectors的内存大小
SPARK_WORKER_INSTANCES:指的是每台机器上开启的Worker节点的数目
```
* 配置slaves
```bash
cp slaves.template slaves
vim slaves
将里面的localhost改成worker01(在配置hadoop是编辑过/etc/hosts)
```
* 启动并测试集群的状况
```bash
  当前我们只是用了hadoop的hdfs文件系统，所以可以只启动hadoop的HDFS文件系统。
cd /usr/local/hadoop-2.7.3/sbin
./start-dfs.sh
可以看到master启动了NameNode  SecondaryNameNode ResourceManager，worker01启动了DataNode，表示HDFS文件系统启动成功
  将配置好的spark文件夹和.bashrc中新增的环境变量配置到worker01中
Warning：位置和目录名一定要匹配,配置完成一定要测试一下
如果是U盘copy过去的可能是没有执行的权限的
chmod -R 755 /usr/local/spark-2.0.1/bin
chmod -R 755 /usr/local/spark-2.0.1/sbin
 用Spark的sbin目录下的start-all.sh,启动Spark集群，这里需要注意的是Hadoop的sbin设置了环境变量而且里面有一个start-all.sh，所以一定要进入spark的sbin目录下执行./start-all.sh
cd  /usr/local/spark-2.0.1/sbin
./start-all.sh
```

* 使用jps命令查看服务的运行情况
```
master：
     NameNode 
     SecondaryNameNode
     ResourceManager
     Jps
     Master
worker:
     Jps
     DataNode
     Worker
```
# 本地Web服务测试
```
在master上
localhost:8080
localhost:50070
localhost:8080（进入网页，可以看到workers下面有一台主机）
```

