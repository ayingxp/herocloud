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
`cd /usr/local/spark && ./bin/pyspark --master local[4] --jars code1.jar,code2.jar,code3.jar`