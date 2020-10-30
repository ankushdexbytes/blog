# How to create a surrogate key with Apache Spark

 **What does  Surrogate Key  mean?**
 A surrogate key (or synthetic key, pseudokey, entity identifier, system-generated key, database sequence number, factless key, technical key, or arbitrary unique identifier) in a database is a unique identifier for either an entity in the modeled world or an object in the database. The surrogate key is not derived from application data, unlike a natural (or business) key which is derived from application data.
 **Surrogate key in a Data Warehouse**: Surrogate keys are typically meaningless integers used to connect the fact to the dimension tables of a data warehouse. There are various reasons why we cannot simply reuse our existing natural or business keys.

# Let's examine what are the options available in Spark

 - **monotonically_increasing_id :** Spark dataframe add unique number is very common requirement especially if you are working on ELT in Spark. You can use monotonically_increasing_id method to generate long number which is monotonically increasing and unique, but not consecutive.
 
 

	>  **Spark Doc :** The generated ID is guaranteed to be monotonically increasing and unique, but not consecutive. The current implementation puts the partition ID in the upper 31 bits, and the record number within each partition in the lower 33 bits. The assumption is that the data frame has less than 1 billion partitions, and each partition has less than 8 billion records.
	
	
	
	**Let’s create one job and generate surrogate keys**
	
	```scala
	 def run(args: Array[String]): Unit = {

	    val spark = SparkSession
	      .builder()
	      .master(args(0))
	      .config("spark.sql.warehouse.dir", System.getProperty("user.dir") + "/spark-warehouse")
	      .enableHiveSupport()
	      .getOrCreate()


	    val articles = loadDataFromSource(spark)

	    val attachSurrogateKey = articles.withColumn("sk", functions.monotonically_increasing_id())

	    attachSurrogateKey.write.mode(SaveMode.Overwrite).saveAsTable("articles_tbl")

	  }

	```
	After running this job surrogate keys will generate. But in ETL jobs we going to be updating the data in batches, maybe a million at a time, maybe 1000 at a time. So we want to see how this surrogate key generation performs over multiple inserts.

	> Run the same job one more time and see how surrogate keys are generated : so when we run the same job again it generates the duplicate surrogate keys.

	**Lets understand with Example**: 
	

 - In First run we insert 1million records and spark generates unique 1million surrogate keys.
 - In Second run we insert 1 million records with append mode it generates duplicates surrogates keys.
 
	**What is the reason for this massive amount of surrogates keys collisions/duplication ?**
	The thing is with monotonically increasing ID is it, it returns, you know, for a given spark job, it returns a number between zero and some upper bound. And it only guarantees that the numbers are increased monotonically. You know, over the data frame there are no other guarantees that there's like no guarantees in terms of the spacing between the numbers, and so on. So we're basically generating the same range of integers or bi gens again, twice. So that's why we have 100. And in fact, it again, there's no guarantee you'll generate the same numbers. But there's no guarantee either that won't generate the same, if that makes any sense. But anyways, so that was kind of a failure.
 
 
 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3MjgwNzY5NjksMjY3MTM2MzksMTkzNz
A1NTg5NiwzNTEyMzY0NDQsLTEyNzkwMzAwNjksMzYzMDQ5Mjk1
LC0yMTIyNDU4MTAyLC05MDk3NzQzMTAsMTE0NzY1NDgzLC01NT
g5MDgwNzcsLTEwNDg0NzU5NDUsLTIwODg3NDY2MTIsLTQ1Mjgw
MjA0NCw2MzcyMTgzODcsMTM3MDcwMzI0NSwxMDc3MjYyMjU5LD
I1NjYyMDg0NCwxMDk2MTUyNjksLTM5NzczNzkzNSwyMDE2OTEx
MTcwXX0=
-->