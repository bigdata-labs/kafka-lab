### list topics:
./bin/kafka-topics.sh --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181 --list

### create topic
bin/kafka-topics.sh --create --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181 --replication-factor 2 --partitions 2 --topic tomcatAccessTopic

### describe topic
./bin/kafka-topics.sh --describe --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181 --topic tomcatAccessTopic

### create producer
./bin/kafka-console-producer.sh --broker-list kafka1:9091,kafka1:9092,kafka3:9093,kafka4:9094 --topic tt


### create consumer
./bin/kafka-console-consumer.sh  --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181 --from-beginning --topic tomcatAccessTopic



java -cp 'spark-stream-kafka-example_2.10-1.0.jar:libs/*' TomcatAccessMessageProducer kafka1:9091,kafka2:9092,kafka2:9093,kafka4:9094 tomcatAccessTopic /usr/local/kafka/access_log.txt

<!-- sbt clean assembly package && scp /Users/jack/codebase/open-sources/spark-vagrant/provision/files/spark/spark-stream-kafka-example/target/scala-2.10/spark-stream-kafka-example_2.10-1.0.jar cloud@kafka1:/usr/local/kafka -->

###生产数据
java -cp 'spark-stream-kafka-example_2.10-1.0.jar:libs/*' TomcatAccessMessageProducer kafka1:9091,kafka1:9092,kafka2:9093,kafka2:9094 tomcatAccessTopic /usr/local/kafka/access_log.txt

### 在spark shell 上测试spark streaming integrate with kafka
./bin/spark-shell --master spark://spark-master:7077



bin/spark-shell  --master spark://spark-master:7077 --jars $SPARK_HOME/lib/kafka_2.10-0.9.0.1.jar,$SPARK_HOME/lib/kafka-clients-0.9.0.1.jar,$SPARK_HOME/lib/spark-streaming-kafka_2.10-1.6.0.jar,$SPARK_HOME/lib/spark-streaming-kafka-assembly_2.10-1.6.0.jar --num-executors 1  --driver-memory 2g  --executor-memory 2g  --executor-cores 1


bin/spark-submit  --master spark://spark-master:7077 --jars $SPARK_HOME/lib/kafka_2.10-0.9.0.1.jar,$SPARK_HOME/lib/kafka-clients-0.9.0.1.jar,$SPARK_HOME/lib/spark-streaming-kafka_2.10-1.6.0.jar,$SPARK_HOME/lib/spark-streaming-kafka-assembly_2.10-1.6.0.jar --num-executors 1  --driver-memory 2g  --executor-memory 2g  --executor-cores 1 --class  TomcatAccessProcessing /usr/local/spark/spark-stream-kafka-example_2.10-1.0.jar

import org.apache.log4j.{Level, Logger}

import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Seconds, StreamingContext, _}


val zkServers= "zookeeper1:2181,zookeeper2:2181,zookeeper3:2181"
val ssc = new StreamingContext(sc, Seconds(2))

val kafkaStream = KafkaUtils.createStream( ssc, zkServers, "group", Map("tomcatAccessTopic" -> 3))
val msgDataRDD = kafkaStream.map(_._2)
 msgDataRDD.print()
    val lines = msgDataRDD.map { line => line }
    lines.foreachRDD(rs => println("========================APL=================\n" + rs))
    ssc.start()
ssc.awaitTermination()









import org.apache.log4j.{Level, Logger}
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Duration, Seconds, StreamingContext}
import org.apache.spark.{HashPartitioner, SparkConf}


val zkServers= "zookeeper1:2181,zookeeper2:2181,zookeeper3:2181"
val ssc = new StreamingContext(sc, Seconds(2))

val kafkaStream = KafkaUtils.createStream( ssc, zkServers, "group", Map("tomcatAccessTopic" -> 3))
val msgDataRDD = kafkaStream.map(_._2)

    ssc.checkpoint("hdfs://hadoop-namenode:7077/user/spark/")

 val popularityData = msgDataRDD.map { line => {
      val dataArr: Array[String] = line.split(" ")
      val ip = dataArr(0)
      //calculate the popularity value
      val count: Double = 1
      (ip, count)
    }
    }
    //sum the previous popularity value and current value
    val updatePopularityValue = (iterator: Iterator[(String, Seq[Double], Option[Double])]) => {
      iterator.flatMap(t => {
        val newValue: Double = t._2.sum
        val stateValue: Double = t._3.getOrElse(0);
        Some(newValue + stateValue)
      }.map(sumedValue => (t._1, sumedValue)))
    }

    val initialRDD = ssc.sparkContext.parallelize(List(("page1", 0.00)))
    val stateDstream = popularityData.updateStateByKey[Double](updatePopularityValue,
 new HashPartitioner(ssc.sparkContext.defaultParallelism), true, initialRDD)
    //set the checkpoint interval to avoid too frequently data checkpoint which may
    //may significantly reduce operation throughput
    stateDstream.checkpoint(Duration(8 * 1 * 1000))
    //after calculation, we need to sort the result and only show the top 10 hot pages
    stateDstream.foreachRDD { rdd => {
      val sortedData = rdd.map { case (k, v) => (v, k) }.sortByKey(false)
      val topKData = sortedData.take(10).map { case (v, k) => (k, v) }
      topKData.foreach(x => {
        println(x)
      })
    }
    }









    ssc.start()
ssc.awaitTermination()



    wget -c http://central.maven.org/maven2/com/101tec/zkclient/0.7/zkclient-0.7.jar



















import org.apache.log4j.{Level, Logger}
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Duration, Seconds, StreamingContext}
import org.apache.spark.{HashPartitioner, SparkConf}


    val zkServers= "zookeeper1:2181,zookeeper2:2181,zookeeper3:2181"
val ssc = new StreamingContext(sc, Seconds(2))
    ssc.checkpoint("hdfs://hadoop-namenode:9000/user/spark/")

val kafkaStream = KafkaUtils.createStream( ssc, zkServers, "group", Map("tomcatAccessTopic" -> 3)).map(_._2)

    val words = kafkaStream.flatMap(_.split(" "))
val wordCounts = words.map(x => (x, 1L)).reduceByKeyAndWindow(_ + _, _ - _, Minutes(10), Seconds(2), 2)
   wordCounts.print()


    ssc.start()
    ssc.awaitTermination()
