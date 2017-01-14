### 不规则格式的数据操作
### 准备
参照10-(1)
### 代码编写
```scala
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
    //print the schema in a tree format
    df.printSchema()
    //查询name列    
    df.select("name").show()
    //查询name,age两列，且将age加1
    df.select($"name",$"age"+1).show()
    //查询年龄大于21的数据
    df.filter($"age">21).show()
    //通过各个年龄的人数
    
    df.gourpBy("age").count().show()
    }
}
```
    




### 输出效果
`df.show()`
age | name
---|---
null | Michael
30 | Andy
19 | Justin
 `df.printSchema()`
```
root
  |-- age:long(nullable=true)
  |-- name:string(nullable=true)
```
`df.select("name").show()`
name |
---
Michael | 
Andy    |
Justin  |
`df.select($"name",$"age"+1).show()`
name | (age+1)
---|---
Michael | null 
Andy |  31
Justin | 20

`df.filter($"age">21).show()`


age | name
---|---
30 | Andy

`df.gourpBy("age").count().show()`

age | count
---|---
19 | 1
null | 1
30 | 1











