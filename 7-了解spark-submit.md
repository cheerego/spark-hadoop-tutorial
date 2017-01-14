### spark-submit
Spark Submit是目前最常用提交Spark应用给集群的工具，其最基本的格式是:  
`spark-submit [option] <app jar|python file>[app option]`  
如:
```bash
spark-submit --master spark://192.168.1.6:7077 --class org.apache.spark.examples.SparkPi /usr/local/spark-2.0.1/examples/jars/spark-examples-2.11-2.0.1.jar

spark-submit --master spark://master:7077 --class SparkPi ~/hello-scala.jar
# 必选参数
# master master url 本例子中使用可以使'master'或者是IP地址，端口号可以通过http://192.168.1.6:8080查看
# class 主类名称，含包名
# 可选参数
# --executor-core executor是用的总核数，也可以在spark/conf/spark-env.sh中设置

```

#### 如何生成jar包，在下节中查看