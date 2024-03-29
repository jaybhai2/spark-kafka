# package application using sbt 

## below is the file structure you need to create
```
project
  -build.sbt
  -src
    -main
      -scala
        -test.scala
```

### in build.sbt manage your dependency 
```
name := "spark Test App"
version := "0.1"
organization := "com.jay.app"
scalaVersion := "2.11.8"
val sparkVersion = "2.2.0"

//%% means spark-core_scalaVersion, with single % you have to append _2.xx after spark-core
libraryDependencies ++= Seq(
  "org.apache.spark" %% "spark-core" % sparkVersion,
  "org.apache.spark" %% "spark-sql" % sparkVersion,
   "org.apache.spark" %% "spark-mllib" % sparkVersion,
  "org.apache.spark" %% "spark-streaming" % sparkVersion,
  "org.apache.spark" %% "spark-hive" % sparkVersion,
  "io.confluent" % "kafka-avro-serializer" % "3.1.1"
)

resolvers += "confluent" at "http://packages.confluent.io/maven/"
```

### test.scala
```
package com.jay.app.examples

import org.apache.spark.sql._
import org.apache.spark.sql.types._

object SparkTestApp {
 def main(args: Array[String]) = {
  val spark = SparkSession.builder()
  .appName("SparkTestApp")
  .enableHiveSupport()
  .getOrCreate()


  //Create a local function
  val parseOS = (s: String) => {
   if (List("apple","app","iphone","ipad","mac").contains(s.toLowerCase))
    "IOS"
   else
    "Android"
  }

  //Register the function as UDF
  spark.udf.register("parseOS", parseOS)
  spark.udf.register("strlen", (s: String) => s.length)
  spark.udf.register("priceGroup", (p:Int ) => if (p > 1000) "High" else "Low")


  spark.sql("""select *, strlen(state),parseOS(devicemake) as operatingsystem from hivesampletable limit 5""")
   .write
   .format("csv")
   .save("file:///home/sshuser/out/")
   spark.stop()
  }
 }

```

Run 'sbt package' on project root directory, then jar file will be created at target folder

```
spark-submit --master local --class com.jay.app.examples.SparkTestApp target/scala-2.11/spark-test-app_2.11-0.1.jar InputIfAny
```

