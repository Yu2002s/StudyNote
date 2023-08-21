##### 常用命令行

nohup command & 创建进程持续运行

tail -f -n 10 nohup.out  查看日志

kill -9 22899 杀死 进程的pid ，关闭程序。

cat info.log 查看文件

ps -ef | grep java 查看java 运行的几个进程 pid

clear 清除命令行

```bash
# 清空命令行
clear 
# 查看文件内容
cat xxx
# 查看IP
ip addr show
ip a
hostname -I
# 查看当前系统时间
date
# 注销
logout
# 关机
shutdown
# 重启
reboot
# 清屏
clear
# 查看当前目录下的文件
ls
# 查看指定目录下的文件
ls /
# 查看详细信息
ls -l
ll
# 查看隐藏文件
ls -a
#参数并用
ls -la
# 指定目录新建文件
mkdir -p 文件名
whereis mysql # 查看软件安装路径
which mysql # 查看mysql的运行路径
```

###### 更新mysql gpg key

```bash
rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
```





#### 安装MySql

官方文档地址 [MySQL ：： MySQL 8.0 参考手册 ：： 2 安装和升级 MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)

<!--Ubuntu跳转第6步-->

###### 1.检查是否已经安装mysql

`rpm -qa|grep mysql`

![image-20221205161315432](D:\jdy2002\Note\学习笔记\image\image-20221205161315432.png)

###### 2.查看mysql当前状态

`service mysqld status`

![image-20221205154545016](C:\Users\jdy2002\AppData\Roaming\Typora\typora-user-images\image-20221205154545016.png)

###### 3.停止mysql服务

`service mysqld stop`

###### 4.卸载mysql

`rpm -ev [需要移除组件的名称]`

还需要移除服务

`yum remove mysql-server`

![image-20221205161558816](D:\jdy2002\Note\学习笔记\image\image-20221205161558816.png)

强制卸载

`rpm -e --nodeps [需要移除组件的名称]`

###### 5.使用rpm来安装MySQL

<!--Ubuntu无需下载-->

镜像源链接：[http://repo.mysql.com/ ](http://repo.mysql.com/)  选择mysql8进行下载

`wget http://repo.mysql.com/mysql80-community-release-el7-1.noarch.rpm`

下载地址默认保存在当前目录, 添加-O参数可以进行设置保存路径

切换到Root账户

`su root`

进行安装

`rpm -ivh mysql80-community-release-el7-1.noarch.rpm`

###### 6.通过yum命令安装

`yum install mysql-server`

`apt install mysql-server #Ubuntu`

###### 7.检查是否启动

`systemctl list-unit-files|grep mysqld`

![image-20221205162831710](D:\jdy2002\Note\学习笔记\image\image-20221205162831710.png)

服务是否已开启

`ps -ef|grep mysql`

启动服务

`systemctl start mysqld.service`

###### 8.进行初始化

`mysqld --initialize`

查看默认密码

`grep 'temporary password' /var/log/mysqld.log`

复制 root@localhost: 后面的密码。登录mysql，并粘贴默认密码

`mysql -u root -p`

直接粘贴密码

###### 9.修改密码

`alter user 'root'@'localhost' identified by '12345678';`

降低密码等级检查

`set global validate_password.policy=0;`

设置密码等级策略

`set global validate_password.policy=0;` 

`set global validate_password.length=1;`

加入权限

`grant system_user on *.* to 'root';`

###### 10.支持远程链接

**修改配置文件**

`cd /etc/mysql/`

或

`cd /etc/mysql/mysql.conf.d`

使用vim进行编辑配置文件

`vim mysqld.conf`

**bindadress = 修改为 0.0.0.0**

------

第一种方法修改root账户host

`use mysql;`
`#修改root账户权限`
`update user set host = '%' where user = 'root';`
`#刷新权限`
`flush privileges;`

------

第二种方法添加远程访问

添加一个账户用于远程访问

`use mysql;` 选择数据库

`create user 'admin'@'%' identified by 'password'; ` 构建用户

`GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%'; #执行授权`

`flush privileges; # 刷新用户权限`

`ALTER USER 'admin'@'%' IDENTIFIED WITH mysql_native_password BY 'password'; ` #授权远程访问

`flush privileges; #刷新`

###### 11.端口设置

`firewall-cmd --zone=public --add-port=3306/tcp --permanent`

重启防火墙

`systemctl restart firewalld.service`

查询防火墙开放端口

`firewall-cmd --list-ports`
