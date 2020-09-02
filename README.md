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

