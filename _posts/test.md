

**Spark runs multiple tasks in parallel but not multiple jobs:**
When a spark action is invoked, a spark job comes into existence which consists of one or more stages and further these stages are broken down into numerous tasks which are worked upon by the executors in parallel but spark not run multiple jobs in parallel when multiple action are called.**

**NOTE: We used the word ‘job’ for ‘spark action’.**

## **Let's do some Data Exploration (EDA) :**

 Let's create simple spark batch job to do some data exploration. We have Netflix sample data with Actors and TV Shows.
```scala
 def run(args: Array[String]): Unit = {

    val spark=SparkSession
      .builder()
      .master(args(0))
      .getOrCreate()

    val NFShow=loadNetflixTVShowData(spark)

    val NFActor=loadNetflixActorData(spark)

    println("Joining up the Actors and TV shows")
    NFShow.join(NFActor,NFActor.col("actor_id") === NFShow.col("actor_id"),"inner")
      .take(10)
      .foreach(println)

    println("Grouping TV shows genre and calculating the count")
    NFShow.groupBy(NFShow.col("genre"))
      .count()
      .take(10)
      .foreach(println)

  }
``` 

 **What happens in the spark when we kick off the exploration ?**
  
- Spark creates separate two jobs. One job for Joining the data and another for grouping the data. 
-   Spark runs the jobs serially.
  - Jobs are independent from each but spark runs in serially.

## Can spark run the above jobs at the same time ?
Spark can run multiple parallel jobs simultaneously. By running concurrent jobs with a single spark session, will not only maximise the resource utilisation but also reduce application time and cost drastically. Furthermore, if we have adequate resources and these jobs do not have any interlink between them then it does not make sense to execute them in a loop or as a different spark applications.
> Multiple parallel jobs can run simultaneously if they were submitted from
> separate threads. By “job”, in this section, we mean a Spark action
> (e.g. `save`, `collect`) and any tasks that need to run to evaluate
> that action.

## Parallel job submission:

To achieve concurrency at job level, we can leverage Scala concurrency features called [_Scala Futures_](https://docs.scala-lang.org/overviews/core/futures.html). Lets rework our above code using Scala futures to parallelize the job submission.
```scala
def run(args: Array[String]): Unit = {

    val spark=SparkSession
      .builder()
      .master(args(0))
      .getOrCreate()

    val NFShow=loadNetflixTVShowData(spark)

    val NFActor=loadNetflixActorData(spark)

    println("Joining up the Actors and TV shows")
    val joinFuture=Future {
      NFShow.join(NFActor, NFActor.col("actor_id") === NFShow.col("actor_id"), "inner")
        .take(10)
        .foreach(println)
    }
    println("Grouping TV shows genre and calculating the count")
    val groupFuture=Future {
      NFShow.groupBy(NFShow.col("genre"))
        .count()
        .take(10)
        .foreach(println)
    }

    Await.ready(joinFuture,Duration.Inf)
    Await.ready(groupFuture,Duration.Inf)

  }
```


<!--stackedit_data:
eyJoaXN0b3J5IjpbMzc1OTYxMzIzLDEwOTYxNTI2OSwtMzk3Nz
M3OTM1LDIwMTY5MTExNzAsLTEzMTA0MDE5MDAsMTYxMDE4Nzc1
NSwtNjE4NTc2NzM1LC0xODA1NjA5MDQ3LC03NDczMDQ0MDUsLT
E5NjUyMDY2MywtMjA4ODc0NjYxMiwtMTAzMzU3NzE3MCw5NTM3
NzE5NTgsMzUwNjc5MzMxLDU4NzYxNjU3LDM2MjkxNTc3MSwxND
g4MzQ1ODIwLC00OTMzMjM2MjUsLTEyNzg0NjY3NywtOTk5MDMw
MzIyXX0=
-->