spark-submit --packages org.apache.spark:spark-streaming-kafka-0-10_2.11:2.4.0  --class "DirectKafkaWordCount" --master local target/scala-2.11/spark-kafka-project_2.11-1.0.jar localhost:9092 grp_nm test > stdout.log

spark-submit --packages org.apache.spark:spark-streaming-kafka-0-10_2.11:2.4.0  --class "DirectKafkaWordCount" --master local target/scala-2.11/spark-kafka-project_2.11-1.0.jar localhost:9092 grp_nm test > stdout.log 2>&1


spark-submit --packages org.apache.spark:spark-streaming-kafka-0-10_2.11:2.4.0  --class "DirectKafkaWordCount" --master local target/scala-2.11/spark-kafka-project_2.11-1.0.jar localhost:9092 grp_nm test &> stdout.log