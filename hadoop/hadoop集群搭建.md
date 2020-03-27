# hadoop集群搭建

`厦门大学数据库实验室参考资料: http://dblab.xmu.edu.cn/blog/1177-2/`
<br>

## 机器准备
- master(192.168.0.31)
- slave01(192.168.0.30)
- slave02(192.168.0.29)

## 创建用户
`分别在３台机器上创建hadoop用户`<br>

`创建用户命令:`
`adduser hadoop`

## 主机相关配置
- 更改主机名
    - `sudo vim /etc/hostname`
    - `hostname文件中的内容分别改为master,slave01, slave02`
