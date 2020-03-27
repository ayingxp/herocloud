# hadoop集群搭建

`厦门大学数据库实验室参考资料: http://dblab.xmu.edu.cn/blog/1177-2/`
<br>

## 机器准备
- master(192.168.0.29)
- slave01(192.168.0.30)
- slave02(192.168.0.31)

## 云服务器ssh连接超时问题
```
#vi /etc/ssh/sshd_config，添加如下两行
ClientAliveInterval 60
ClientAliveCountMax 86400
```

## 创建用户
`分别在３台机器上创建hadoop用户`<br>

`创建用户命令:`
`adduser hadoop`

## 主机相关配置
- 更改主机名
    - `sudo vim /etc/hostname`
    - `hostname文件中的内容分别改为master,slave01, slave02`

- 添加域名映射(为了集群机器间通过别名访问)
    - 每台机器上/etc/hosts文件中添加如下内容<br>
    `127.0.0.1 localhost`<br>
    `192.168.0.29 master`<br>
    `192.168.0.30 slave01`<br>
    `192.168.0.31 slave02`<br>

- 配置ssh通过主机名来连接
    - `ssh-keygen`
        - `cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys`
    - 让master主机免密码登录slave01和slave02主机
        - scp ~/.ssh/id_rsa.pub hadoop@slave01:/home/hadoop/
        - scp ~/.ssh/id_rsa.pub hadoop@slave02:/home/hadoop/
        - cat ~/id_rsa.pub >> ~/.ssh/authorized_keys

## JDK和Ｈadoop安装配置

### 安装JDK
`分别在master主机和slave01\slave02主机上安装JDK`
- `sudo apt-get install default-jdk`
- `编辑~/.bashrc文件，添加如下内容`
    - `export JAVA_HOME=/usr/lib/jvm/default-java`
- `接着让环境变量生效，执行如下代码：`
    - `source ~/.bashrc`

### 安装hadoop
`先在master主机上做安装Hadoop，暂时不需要在slave01,slave02主机上安装Hadoop.稍后会把master配置好的Hadoop发送给slave01,slave02.`
- `在master主机执行如下操作`
    - `sudo tar -zxf ~/下载/hadoop-2.7.3.tar.gz -C /usr/local    # 解压到/usr/local中`
    - `cd /usr/local/`
    - `sudo mv ./hadoop-2.7.3/ ./hadoop            # 将文件夹名改为hadoop`
    - `sudo chown -R hadoop ./hadoop       # 修改文件权限`

    - `编辑~/.bashrc文件，添加如下内容：`
        - `export HADOOP_HOME=/usr/local/hadoop`
        - `export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin`
    - `接着让环境变量生效:`
        - `source ~/.bashrc`


### hadoop集群配置
`修改master主机的Hadoop如下配置文件，这些配置文件都位于/usr/local/hadoop/etc/hadoop目录下。
修改workers:`
<br>
<br>

`这里把DataNode的主机名写入该文件，每行一个。这里让master节点主机仅作为NameNode使用。`

`slave01`<br>
`slave02` <br>

- 修改core-site.xml
```
 <configuration>
      <property>
          <name>hadoop.tmp.dir</name>
          <value>file:/usr/local/hadoop/tmp</value>
          <description>Abase for other temporary directories.</description>
      </property>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://master:9000</value>
      </property>
  </configuration>
```

- 修改hdfs-site.xml
`在集群的结点上创建/var/lib/hadoop/hdfs/name和/var/lib/hadoop/hdfs/data目录`<br>
`将/var/lib/hadoop目录的权限赋给hadoop用户`<br>

```
 <configuration>

    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/var/lib/hadoop/hdfs/name/</value>
    </property>

    <property>
        <name>dfs.datanode.data.dir</name>
        <value>/var/lib/hadoop/hdfs/data/</value>
    </property>

    <property>
        <name>dfs.replication</name>
        <value>3</value>
    </property>
    </configuration>
```

- 修改mapred-site.xml(复制mapred-site.xml.template,再修改文件名)
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
  </configuration>
```

- 修改yarn-site.xml
```
<configuration>
  <!-- Site specific YARN configuration properties -->
      <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value>
      </property>
      <property>
          <name>yarn.resourcemanager.hostname</name>
          <value>master</value>
      </property>
  </configuration>
```

- 复制到其他结点
```
配置好后，将 master 上的 /usr/local/Hadoop 文件夹复制到各个节点上。之前有跑过伪分布式模式，建议在切换到集群模式前先删除之前的临时文件
```

```
在 master 节点主机上执行：
cd /usr/local/
rm -rf ./hadoop/tmp   # 删除临时文件
rm -rf ./hadoop/logs/*   # 删除日志文件
tar -zcf ~/hadoop.master.tar.gz ./hadoop
cd ~
scp ./hadoop.master.tar.gz slave01:/home/hadoop
scp ./hadoop.master.tar.gz slave02:/home/hadoop
```

- 在slave01,slave02节点上执行：
```
sudo rm -rf /usr/local/hadoop/
sudo tar -zxf ~/hadoop.master.tar.gz -C /usr/local
sudo chown -R hadoop /usr/local/hadoop
```


### 启动集群

```
在master主机上执行如下命令:
cd /usr/local/hadoop
bin/hdfs namenode -format
sbin/start-all.sh
sbin/start-yarn.sh

运行后，在master，slave01,slave02运行jps命令，查看：
jps
```