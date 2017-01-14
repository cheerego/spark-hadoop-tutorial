### JDBC to Other Databases
Spark SQL 也可以通过JDBC读取数据从其他的数据库。这个功能首选使用JdbcRDD，因为结果集可以作为DataFrame返回，且在Spark SQL中容易被处理和加入到其他的数据源中
。