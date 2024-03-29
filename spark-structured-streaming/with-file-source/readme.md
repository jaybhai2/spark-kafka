```
import org.apache.spark.sql.functions._ 
import org.apache.spark.sql.types._ 
import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder
  .appName("transactionobserver")
  .getOrCreate()
  

val mySchema = new StructType().add("sender", StringType).add("receiver", StringType).add("amount", DoubleType).add("_corrupt_record",StringType)

val sDF = spark
  .readStream
  .option("sep", ",")
  .schema(mySchema)      // Specify schema of the csv files
  .csv("/home/jay/data/streaming/")    // Equivalent to format("csv").load("/path/to/directory")

val sDF2 = sDF.select(initcap(trim($"sender")) as "sender",
            initcap(trim($"receiver")) as "receiver", 
            format_number($"amount",2) as "amount", 
            date_format(current_timestamp(),"HH:mm:ss MM-dd") as "timestamp",
            $"_corrupt_record")

//case class transaction(sender: String, receiver: String, timestamp: Double, time: java.sql.Timestamp)
//sDS = sDF2.as[transaction]


val query = sDF2.writeStream
  .outputMode("append") //when there is not aggregation, you can not use complete mode
  .format("csv") //.format("console")
  .option("checkpointLocation", "/home/jay/data/streaming_checkpoint/") // checkpoint should be hdfs location to achieve fault tolerance
  .start("/home/jay/data/streaming_out/")

query.awaitTermination()
```
consolde mode

![Image](https://github.com/jayjayjohn/spark-kafka/blob/master/spark-structured-streaming/with-file-source/Capture.PNG)
