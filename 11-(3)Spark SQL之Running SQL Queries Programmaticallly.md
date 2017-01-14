### 使用SQL语句的方式
SparkSession中的`sql()`方法可以shi使程序运行SQL语句和返回DataFrame的结果集。
###代码编写
```
import org.apache.spark.SparkConf
import org.apache.spark.sql.SparkSession

object SparkSQL{
    def main(args:Array[String]):Unit={
        val conf = new SparkConf().setMaster("spark://master:7077").setAppName("SparkSQLDemo")
        val spark = SparkSession
        .builder()
        .config(conf)
        .getOrCreate()
    //这个包用于将RDD隐式转换为DataFrame
    import spark.implicits._
    val df = spark.read.json("/people.json")

    //注册DataFrame ds作为临时的SQL试图
    df.createOrReplaceTempView("people")
    val sqlDF = spark.sql("SELECT * FROM people")
    sqlDF.show()
    }
}
```
### 输出效果

age | name
---|---
null | Michael
30   | Andy
19   | Justin

