https://blog.matthewrathbone.com/2013/01/05/a-quick-guide-to-hadoop-map-reduce-frameworks.html

| id        	| email         | language  |location
| ------------- |:-------------:| :--------:|---
| 1      | jahjah@gmail.com | ENGLISH | US
| 2      | mohan@outlook.com      |   ENGLISH | INDIA
| 3 | sudan@aol.com      |    ENGLISH | UK


| transaction-id| product-id| user-id| purchase-amount| item-description
| -------- |:-------------:| :--------:|---:|---
| 1      | U1 | 1 | $120 | WATCH | Rolex
| 2      | A2 | 2 | $300 | Bike  | NA
| 3      | H2 | 1 | $20 | Hat  | NA
| 4      | H8 | 1 | $5 | Cup  | NA
| 5      | H4 | 1 | $90 | Heel  | NA



```
package main.scala.com.matthewrathbone.spark

import org.apache.spark.SparkContext
import org.apache.spark.SparkContext._
import org.apache.spark.SparkConf
import org.apache.spark.rdd.RDD
import scala.collection.Map

class ExampleJob(sc: SparkContext) {
  // reads data from text files and computes the results. This is what you test
  def run(t: String, u: String) : RDD[(String, String)] = {
    val transactions = sc.textFile(t)
	val newTransactionsPair = transactions.map{t =>                
	    val p = t.split("\t")
	    (p(2).toInt, p(1).toInt)
	}
	
	val users = sc.textFile(u)
	val newUsersPair = users.map{t =>                
	    val p = t.split("\t")
	    (p(0).toInt, p(3))
	}
	
	val result = processData(newTransactionsPair, newUsersPair)
	return sc.parallelize(result.toSeq).map(t => (t._1.toString, t._2.toString))
  } 
  
  def processData (t: RDD[(Int, Int)], u: RDD[(Int, String)]) : Map[Int,Long] = {
	var jn = t.leftOuterJoin(u).values.distinct
	return jn.countByKey
  }
}

object ExampleJob {
  def main(args: Array[String]) {
    val transactionsIn = args(1)
    val usersIn = args(0)
    val conf = new SparkConf().setAppName("SparkJoins").setMaster("local")
	val context = new SparkContext(conf)
    val job = new ExampleJob(context)
    val results = job.run(transactionsIn, usersIn)
    val output = args(2)
    results.saveAsTextFile(output)
    context.stop()
  }
}
```
##Test 
```
  
package test.scala.com.matthewrathbone.spark

import org.scalatest.junit.AssertionsForJUnit
import org.apache.spark.SparkContext
import org.junit.Test
import org.junit.Before
import org.apache.spark.SparkConf
import main.scala.com.matthewrathbone.spark.ExampleJob
import org.junit.After

class SparkJoinsScalaTest extends AssertionsForJUnit {

  var sc: SparkContext = _
  
  @Before
  def initialize() {
    val conf = new SparkConf().setAppName("SparkJoins").setMaster("local")
    sc = new SparkContext(conf)
  }
  
  @After
  def tearDown() {
    sc.stop()
  }
  
  @Test
  def testExamleJobCode() {
    val job = new ExampleJob(sc)
    val result = job.run("./transactions.txt", "./users.txt")
    assert(result.collect()(0)._1 === "1")
    assert(result.collect()(0)._2 === "3")
    assert(result.collect()(1)._1 === "2")
    assert(result.collect()(1)._2 === "1")
  }
}
```
