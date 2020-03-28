# Spark安装

## 下载spark
`从spark官网下载[Pre-built with user-provided apache hadoop]版本的spark`

## 安装
- 解压
`sudo tar -zxf spark-2.4.5-bin-without-hadoop.tgz -C /usr/local/`

- 重命名
`cd /usr/local && sudo mv spark-2.4.5-bin-without-hadoop ./spark`

- 更改目录权限
`sudo chown -R hadoop ./spark`

## 配置spark
- 配置spark的classpath
`cd /usr/local/spark`<br>
`cp ./conf/spark-env.sh.template ./conf/spark-env.sh`

- 编辑spark-env.sh配置文件
`添加: export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop/bin/hadoop classpath)`

- 运行spark
`修改号配置文件后，就可以启动、运行spark了`<br>
`如果需要使用HDFS中的文件，则在使用spark前需要启动hadoop`

## spark部署模式
- Local模式
    - 单机模式
- Standalone模式
    - 使用Spark自带的简单集群管理器
- YARN模式
    - 使用YARN作为集群管理器
- Mesos模式
    - 使用Mesos作为集群管理器

## pyspark
`cd /usr/local/spark && ./bin/pyspark --master local[4]` <br>
`cd /usr/local/spark && ./bin/pyspark --master local[4] --jars code1.jar,code2.jar,code3.jar`<br>

- 配置pyspark路径
`在~/.bashrc中添加 SPARK_HOME 和 PYTHONPATH变量`<br>
`export SPARK_HOME=/usr/local/spark`<br>
`export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.7-src.zip:$PYTHONPATH`<br>
`export PAHT=$PATH:$SPARK_HOME/bin`<br>
`然后运行 source ~/.bashrc命令`


## spark集群环境配置
- 环境变量设置
`在master结点中的~/.bashrc中添加:export SPARK_HOME=/usr/local/spark和export PAHT=$PATH:$SPARK_HOME/bin:$HADOOP_HOME/bin`

- slaves文件配置
`将 slaves.template 拷贝到 slaves:`<br>
`cd $SPARK_HOME && cp ./conf/slaves.template ./conf/slaves`<br>
`slaves文件设置worker结点，编辑slaves内容，把默认内容localhost替换成如下内容: `<br>
`slave01`<br>
`slave02`<br>

- 配置spark-env.sh文件
```
将spark-env.sh.template拷贝到spark-env.sh:

cp ./conf/spark-env.sh.template ./conf/spark-env.sh

编辑spark-env.sh文件，添加如下内容:
export SPARK_DIST_CLASSPATH=$(/usr/local/hadoop classpath)
export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop
export SPARK_MASTER_IP=192.168.0.29
```

- 配置slaves结点
```
配置号master结点的spark后,将master主机上的/usr/local/spark文件夹复制到各个结点上.
在master结点上执行如下命令：

cd /usr/local
tar -zcf ~/spark.master.tar.gz ./spark
cd ~
scp ./spark.master.tar.gz slave01:/home/hadoop
scp ./spark.master.tar.gz slave02:/home/hadoop

在slave01和slave02上分别执行下面的操作:

sudo  rm -rf /usr/local/spark
sudo tar -zxf ~/spark.master.tar.gz -C /usr/local
sudo chown -R hadoop /usr/local/spark
```

- 启动spark集群
```
1. 首先要启动Ｈadoop集群,在master结点主机上运行如下命令:
cd /usr/local/hadoop
./sbin/start-all.sh

2. 启动Master结点，在master结点主机上运行如下命令:
cd /usr/local/spark
./sbin/start-master.sh

3.启动所有slave结点，在master结点主机上运行如下命令:
./sbin/start-slaves.sh
```

- 关闭spark集群
```
在master结点上执行如下命令:
1. 关闭master结点
./sbin/stop-master.sh

2. 关闭worker结点
./sbin/stop-slaves.sh

3. 关闭Hadoop集群
cd /usr/local/hadoop
./sbin/stop-all.sh
```

## 在集群上运行Spark应用程序
### 启动Spark集群
```
启动hadoop集群:
cd /usr/local/hadoop
./sbin/start-all.sh

启动Spark的master结点和所有slaves结点

cd /usr/local/spark
./sbin/start-master.sh
./sbin/start-slaves.sh

```

### 采用独立集群管理器
- 在集群中运行应用程序
```
向独立集群管理器中提交应用，需要把spark://master:7077作为主结点参数传递给spark-submit.

以spark自带的样例程序SparkPi（计算圆周率）为例子：

cd /usr/local/spark
./bin/spark-submit --master spark://master:7077 \
/usr/local/spark/examples/src/main/python/pi.py
```

- 在集群中运行pyspark
`也可以用pyspark连接到独立集群管理器上`<br>
```
cd /usr/local/spark
./bin/pyspark --master spark://master:7077

===============================================
textFile=sc.textFile("hdfs://master:9000/README.md")
textFile.count()
textFile.first()
```

- 查看集群信息
```
用户在独立集群管理web界面查看应用的运行情况:
http://master:8080/

```


### 采用Ｈadoop Yarn管理器

- 在集群中运行应用程序
```
向hadoop yarn 集群管理器提交应用， 需要把yarn-client或者yarn-cluster(测试可行)作为主结点参数传递给spark-submit

cd /usr/local/spark
./bin/spark-submit --master yarn-cluster \
/usr/local/spark/examples/src/main/python/pi.py

运行成功后，根据在shell中得到的输出结果地址查看：
http://master:8088/proxy/application_1585382647418_0003/

点击logs 再点击 stdout
```

- 在集群中运行pyspark程序
```
也可以用pyspark连接到采用yarn作为集群管理器的集群上

pyspark --master yarn

假设HDFS的根目录下已经存在一个文件READ.md, 在pyspark中执行如下语句:

textFile=sc.textFile("hdfs://master:9000/README.md")
textFile.count()
textFile.first()
```

- 查看集群信息
```
用户在hadoop yarn 集群管理web 界面查看所有应用的运行情况:
http://master:8088/cluster
```

## web ui
- spark master
    - http://master:8080/
- hadoop cluster
    - http://master:8088/