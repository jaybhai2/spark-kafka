wget https://piccolo.link/sbt-1.3.8.tgz

tar -zvxf sbt-1.3.8.tgz

vi ~/.profile
source ~/.profile

![Image1](https://github.com/jayjayjohn/spark/blob/master/reference/install_sbt/Capture.PNG)

mkdir sbt_workspace # to store all your sbt project

mkdir sbt_test_project 

cd into test project then vi helloWorld.scala
put bellow code into helloWorld.scala

```
object helloWorld{
          def main(args: Array[String]): Unit = {
          println("Hello, world!")
          }
}
```

sbt run helloWorld.scala

run sbt again to sbt cli

set libraryDependencies += "org.apache.spark" % "spark-core_2.11" % "2.0.2"

show libraryDependencies

session save

exit # build.sbt created

for scala REPL , run: sbt console # if start console in the current project dir, all dependency , object/class will be pulled to scala session

