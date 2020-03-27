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