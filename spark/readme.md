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