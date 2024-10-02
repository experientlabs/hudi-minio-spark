# Spark-DP-101

Spark Data Platform-101 is a Very basic Apache Spark setup in a Docker Container. 
This setup is designed for testing Apache spark and for learning purpose as an alternative to VM's 
which are big in volume and take too much resources. 
This docker application has all basic features of Apache Spark like:
1. Spark Shell 
2. Pyspark Shell 
3. Jupyter Notebook http://localhost:4041
4. Spark UI http://localhost:4040
5. Spark History Server http://localhost:18080

### Architecture

> ![hl_architecture.png](resources/hl_architecture.png)

### How to use it:
#### 1. Clone the repository in your machine using git clone command  
   ```commandline
   git clone git@github.com:experientlabs/spark-dp-101.git
   ```
#### 2. Next build the image by running below `docker build` command.  

   ```commandline
   docker build -t spark-dp-101 
   docker build -t spark-hudi-101 -f dev.Dockerfile .

   ```
   - Here -t is to tag image with a name:`spark-dp-101`.
   - Here '.' is to run the build command in current directory. So dockerfile should be located in current directory.   

#### 3. Once image is built you need to run following command to run the container in jupyter notebook mode. 

   ```commandline
   hostfolder="$(pwd)"
   dockerfolder="/home/sparkuser/app"
   
   docker run --rm -d --name spark-container \
   -p 4040:4040 -p 4041:4041 -p 18080:18080 \
   -v ${hostfolder}/app:${dockerfolder} -v ${hostfolder}/event_logs:/home/spark/event_logs \
   spark-dp-101:latest jupyter
   ```

####  In order to run it in saprk-shell mode use below command (here last parameter is replaced with `spark-shell`). 

   ```commandline
   hostfolder="$(pwd)"
   dockerfolder="/home/sparkuser/app"
   
   docker run --rm -it --name spark-container \
   -p 4040:4040 -p 18080:18080 -p 8080:8080 \
   -v ${hostfolder}/app:${dockerfolder} -v ${hostfolder}/event_logs:/home/spark/event_logs \
   spark-dp-101:latest spark-shell
   ```


```commandline
hostfolder="$(pwd)"
dockerfolder="/home/sparkuser/app"

docker run --rm -it --name spark-uc \
-p 4040:4040 -p 18080:18080 -p 8080:8080 \
-v ${hostfolder}/app:${dockerfolder} -v ${hostfolder}/event_logs:/home/spark/event_logs \
spark-dp-101:latest spark-shell
```

####  Similarly to run pyspark shell  use below command (here last parameter is replaced with `pyspark`). 

   ```commandline
   hostfolder="$(pwd)"
   dockerfolder="/home/sparkuser/app"
   
   docker run --rm -it --name spark-container \
   -p 4040:4040 -p 4041:4041 -p 18080:18080 \
   -v ${hostfolder}/app:${dockerfolder} -v ${hostfolder}/event_logs:/home/spark/event_logs \
   spark-dp-101:latest pyspark
   ```

#### Once your container is running you can use below urls to access various web UI's
1. Jupyter Notebook: http://localhost:4041
2. Spark UI: http://localhost:4040
3. Spark History Server: http://localhost:18080


Terminal window after running docker run command:

> ![terminal.png](resources/terminal.png)
> ![terminal_op.png](resources/terminal_op.png)

### Jupyter Notebook
http://127.0.0.1:4041/notebooks/first_notebook.ipynb
Running below code in jupyter notebook, in order to ascertain that spark is working fine in the container. 
```python
import findspark
findspark.init()
import pyspark
from pyspark.sql import SparkSession
import pyspark.sql.functions as f

# create spark session
spark = SparkSession.builder.appName("SparkSample").getOrCreate()

# read text file
df_text_file = spark.read.text("textfile.txt")
df_text_file.show()

df_total_words = df_text_file.withColumn('wordCount', f.size(f.split(f.col('value'), ' ')))
df_total_words.show()

# Word count example
df_word_count = df_text_file.withColumn('word', f.explode(f.split(f.col('value'), ' '))).groupBy('word').count().sort('count', ascending=False)
df_word_count.show()
```

> ![jupyter.png](resources/jupyter.png)

### Output of word count example: 

> ![jupyter_op.png](resources/jupyter_op.png)


### Spark UI:
http://localhost:4040/jobs/
> ![spark_ui.png](resources/saprk_ui.png)


### Spark History Server: 
http://localhost:18080/
> ![spark_history_server.png](resources/spark_history_server.png)


Above features can also be accessed using docker-compose commands
- docker-compose up jupyter
- docker-compose up spark-shell
- docker-compose up pyspark


This repository is brough to you by ExperientLabs, if you want to contribute, please feel free to raise a PR or if you 
come across an issue, don't hesitate to raise it. 


docker cp /home/sanjeet/Downloads/unitycatalog-0.1.0.tar.gz be3a8857e400:/home/spark/unitycatalog-0.1.0.tar.gz
tar -xf unitycatalog-0.1.0.tar.gz

https://books.japila.pl/unity-catalog-internals/demo/namespace-support-in-spark-integration/



docker-compose -f docker-compose-multinode.yml up











### Error Logs

df.write.format("hudi").options(**hudi_options).mode("overwrite").save("/tmp/hudi_table")
24/09/19 06:58:14 WARN DFSPropertiesConfiguration: Cannot find HUDI_CONF_DIR, please set it as the dir of hudi-defaults.conf
24/09/19 06:58:14 WARN DFSPropertiesConfiguration: Properties file file:/etc/hudi/conf/hudi-defaults.conf not found. Ignoring to load props file
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/home/spark/python/pyspark/sql/readwriter.py", line 1463, in save
    self._jwrite.save(path)
  File "/home/spark/python/lib/py4j-0.10.9.7-src.zip/py4j/java_gateway.py", line 1322, in __call__
  File "/home/spark/python/pyspark/errors/exceptions/captured.py", line 179, in deco
    return f(*a, **kw)
           ^^^^^^^^^^^
  File "/home/spark/python/lib/py4j-0.10.9.7-src.zip/py4j/protocol.py", line 326, in get_return_value
py4j.protocol.Py4JJavaError: An error occurred while calling o71.save.
: org.apache.hudi.exception.HoodieException: hoodie only support org.apache.spark.serializer.KryoSerializer as spark.serializer


hostfolder="$(pwd)"
dockerfolder="/home/sparkuser/app"

docker run --rm -d --name spark-container \
-p 4040:4040 -p 4041:4041 -p 18080:18080 \
-v ${hostfolder}/app:${dockerfolder} -v ${hostfolder}/event_logs:/home/spark/event_logs \
spark-hudi-101:latest jupyter



/home/spark/sbin/start-history-server.sh && pyspark --jars /home/spark/jars/hudi-spark3.5-bundle_2.13-0.15.0.jar



import findspark
findspark.init()
import pyspark
from pyspark.sql import SparkSession
import pyspark.sql.functions as f

# Create SparkSession with Hudi configuration

spark = SparkSession.builder \
    .appName("HudiExample") \
    .config("spark.serializer", "org.apache.spark.serializer.KryoSerializer") \
    .config("spark.sql.extensions", "org.apache.spark.sql.hudi.HoodieSparkSessionExtension") \
    .config("spark.sql.catalog.spark_catalog", "org.apache.spark.sql.hudi.catalog.HoodieCatalog") \
    .getOrCreate()


# Read Hudi data back
hudi_read_options = {
    'hoodie.datasource.query.type': 'snapshot'
}

df_hudi = spark.read.format("hudi").options(**hudi_read_options).load("/home/sparkuser/app/hudi/table/*")
df_hudi.show()


# Adding a new record
new_data = [Row(id=3, name="Charlie", age=28)]
df_new = spark.createDataFrame(new_data).withColumn("curr_timestamp", f.current_timestamp())
df_new.show()

# Write data (upsert)
df_new.write.format("hudi").options(**hudi_options).mode("append").save("/home/sparkuser/app/hudi/table")