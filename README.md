# Elasticsearch & Spark Integration with ES-Hadoop Connector
Connecting Elasticsearch and Spark for Big Data operations using pyspark and ES-Hadoop Connector

This is a guide for people who are using elasticsearch and spark in the same enviroment.(Most of the time, that is the case.)
I will try my best to explain, how to connect elasticsearch and spark using ES-Hadoop Connector.
The reason i am explaining this is, there are limited/unclear resources for users to achieve this properly. And i will explain my steps one by one.

So, lets get started!

# What can we do with it?
> For reference: https://www.elastic.co/guide/en/elasticsearch/hadoop/current/reference.html
  * It lets you flow your data bi-directionally
  * Read from ES => ETL => Write to ES
  * We can run much complicated and personalised ML jobs with Spark on our ES data.
  * And much more..

# What is ES-Hadoop Connector

This is a jar file that contains all the necessary functions to connect elasticsearch with hadoop ecosystem.
You can use this jar to connect hadoop,hive,spark etc. with elasticsearch. For my case, i used this for ES-Spark integration.

# STEP 1 - Download ES-Hadoop Connector

  * Download link: https://www.elastic.co/downloads/hadoop
    > ES-Hadoop Connector has backwards compatibility, So, if your ES version is 7.5, ES-Hadoop can be version 7.8. But, i prefer matching versions.
  * Check your spark version is compatible with your ES-Hadoop Connector (If it is >2, it should be fine.)
    > Reference: https://www.elastic.co/guide/en/elasticsearch/hadoop/current/requirements.html#requirements-spark
  * Check your java version that is used by spark. It should be at least version 1.8.  
    > Reference: https://www.elastic.co/guide/en/elasticsearch/hadoop/current/requirements.html#requirements-jdk
    ```sh
    $ java -version
    ```
  * If you have multiple Java versions, you can change it with "alternatives" (rhel/centos)
    ```sh
    $ sudo alternatives --config java
    ```

# STEP 2 - Unzip ES-Hadoop Connector and copy files to spark jar folder
 * After downloading ES-Hadoop Connector, unzip the files.
    ```sh
    $ unzip elasticsearch-hadoop-7.5.0.zip
    ```
 * After unzipping, there is a "dist" folder inside that includes various .jar files. We only need "elasicsearch-hadoop-7.5.0.jar". This jar includes all other jars.
   > We are copying our jar to "jars" folder inside our "$SPARK_HOME" which is "/etc/spark/" for me.
   > That way, we are more organized.
    ```sh
    $ pwd
    /tmp/elasticsearch-hadoop-7.5.0/dist
    $ cp elasticsearch-hadoop-7.5.0.jar /etc/spark/jars
    ```
    
 # STEP 3 - Configure SparkConf() to use ES-Hadoop Connector
  * Now, we can configure our SparkConf() to use our ES.
   > I am using pyspark with jupyter notebook for this purpose. You can use pyspark-shell or your preference of your choice.
   ```python
    from pyspark import SparkConf
    conf = SparkConf()
    #We can set any other configurations like this. Then pass the "conf" object to SparkSession or SparkContext.
    conf.set("spark.driver.extraClassPath", "/etc/spark/jars/elasticsearch-hadoop-7.5.0.jar")
   ```
# Step 4 - Pass your Elasticsearch Configuration using SparkConf()
 * We are almost there! Now, we can set configurations/options for our ES-Hadoop Connector to connect Elasticsearch.
  > For more configuration, Reference: https://www.elastic.co/guide/en/elasticsearch/hadoop/current/configuration.html
   ```python
    conf.set("es.nodes.discovery", "false") # I am setting this to false, because my spark machine and my es cluster is in different isolated networks.
    conf.set("es.node.data.only", "false") # I will be using my client node to gather data from elasticsearch.
    conf.set("es.nodes.wan.only", "false") # TODO
    conf.set("es.nodes.client.only", "true")
    conf.set("es.nodes", "myesclient:9200") # you can pass multiple machines using comma(,) inside one single string("es1:9200,es2:9200,es3:9200")
    conf.set("es.net.http.auth.user", "spark") # authorized user to read indexes. If you dont have any auth mechanism. You don't need this.
    conf.set("es.net.http.auth.pass", "youruserpassword") # users password 
    
    #now we can create our spark session
    from pyspark import SparkSession
    spark = SparkSession.builder.master("local[4]").appName("your_app_name").config(conf=conf).getOrCreate()
   ```
# Step 5 - Let the fun part begin! Read Operation
 * Now, we can read data from elasticsearch and manipulate the data however we want.
  > Spark Read Reference: https://spark.apache.org/docs/3.0.0/api/python/pyspark.sql.html#pyspark.sql.DataFrameReader.format
  
  > ES Query Reference: https://www.elastic.co/guide/en/elasticsearch/hadoop/current/configuration.html#_querying
 ```python
 #we can define which indexes will be used to read data from
  my_resource = "my_index/_doc" #you dont need to specify '_doc' if you dont use any types in es.
  my_query = '{"query":{"match_all": {}}}'  # you can specify a one-liner dsl query here. also accepts parametric query
  df = spark.read.format("org.elasticsearch.spark.sql").option("es.resource", my_resource).option("es.query", my_query).load()
  
  my_other_resource = "my_other_index/_doc"
  my_other_query = '{"query":{"match":{"exits":{"field": "some_field_here"}}}}'
  other_df = spark.read.format("org.elasticsearch.spark.sql").option("es.resource", my_other_resource).option("es.query", my_other_query).load()
 ```
 > Specifying query and resource as an option variable for read operation allows us to use multiple sources more easily afterwards.
 
 > NOTE: Unfortunately, we can not use aggregations inside elasticsearch query. We need to apply those on spark.
 
#UPCOMING MORE!!!
