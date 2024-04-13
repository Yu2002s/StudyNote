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

adduser # 添加用户
passwd # 设置用户密码
localectl set-locale LANG=zh_CN.UTF8 # 设置语言为中文
```

### Nginx-Go-Access(日志分析器)

安装

```bash
# 安装 goaccess
apt install goaccess
# 格式化日志
goaccess -f xxx.log
```

开启报告分析

```bash
goaccess access.log -a -o ../html/report.html --real-time-html --log-format=COMBINED
```

nginx日志目录: /var/log/nginx/access.html

nginx html根目录：/usr/share/nginx/html

查看报告: 浏览器访问 http://host/report.html

### 防火墙

解决wsl2执行systemctl报错问题

```bash
sudo apt-get install daemonize
sudo daemonize /usr/bin/unshare --fork --pid --mount-proc /lib/systemd/systemd --system-unit=basic.target
exec sudo nsenter -t $(pidof systemd) -a su - $LOGNAME
```

安装防火墙

```bash
apt install firewalld
```

查看防火墙状态

```bash
sudo service status firewalld
firewall-cmd state
```

启动防火墙

```bash
service firewalld start
```

	启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed

3.配置firewalld-cmd

查看版本： firewall-cmd --version

查看帮助： firewall-cmd --help

显示状态： firewall-cmd --state

查看所有打开的端口： firewall-cmd --zone=public --list-ports

更新防火墙规则： firewall-cmd --reload

查看区域信息:  firewall-cmd --get-active-zones

查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0

拒绝所有包：firewall-cmd --panic-on

取消拒绝状态： firewall-cmd --panic-off

查看是否拒绝： firewall-cmd --query-panic

那怎么开启一个端口呢

添加

firewall-cmd --zone=public --add-port=80/tcp --permanent   （--permanent永久生效，没有此参数重启后失效）

重新载入

firewall-cmd --reload

查看

firewall-cmd --zone=public --query-port=80/tcp

删除

firewall-cmd --zone=public --remove-port=80/tcp --permanent

### Nginx

1.安装 ：[nginx: Linux packages](https://nginx.org/en/linux_packages.html#Ubuntu)

2.配置防火墙，开放80端口

3.配置环境变量

4.输入命令nginx启动服务器

5.停止nginx

```bash
nginx -s stop
# 等待请求完成后再停止
nginx -s quite
```

6.重载

```bash
nginx -s reload
```

7.配置路径 **/ect/nginx/nginx.config**

8.检查配置

```bash
nginx -t
```

#### 配置

```nginx
server {
    listen       80;  //监听端口为80，可以自定义其他端口，也可以加上IP地址，如，listen 127.0.0.1:8080;
    server_name  localhost; //定义网站域名，可以写多个，用空格分隔。
    #charset koi8-r; //定义网站的字符集，一般不设置，而是在网页代码中设置。
    #access_log  logs/host.access.log  main; //定义访问日志，可以针对每一个server（即每一个站点）设置它们自己的访问日志。

    ##在server{}里有很多location配置段
    location / {
        root   html;  //定义网站根目录，目录可以是相对路径也可以是绝对路径。
        index  index.html index.htm; //定义站点的默认页。
    }

    #error_page  404              /404.html;  //定义404页面

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;  //当状态码为500、502、503、504时，则访问50x.html
    location = /50x.html {
        root   html;  //定义50x.html所在路径
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #定义访问php脚本时，将会执行本location{}部分指令
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;  //proxy_pass后面指定要访问的url链接，用proxy_pass实现代理。
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;  //定义FastCGI服务器监听端口与地址，支持两种形式，1 IP:Port， 2 unix:/path/to/sockt
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;  //定义SCRIPT_FILENAME变量，后面的路径/scripts为上面的root指定的目录
    #    include        fastcgi_params; //引用prefix/conf/fastcgi_params文件，该文件定义了fastcgi相关的变量
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    # 
    #location ~ /\.ht {   //访问的url中，以/.ht开头的，如，www.example.com/.htaccess，会被拒绝，返回403状态码。
    #    deny  all;  //这里的all指的是所有的请求。
    #}
}


# another virtual host using mix of IP-, name-, and port-based configuration
#
#server {
#    listen       8000;  //监听8000端口
#    listen       somename:8080;  //指定ip:port
#    server_name  somename  alias  another.alias;  //指定多个server_name

#    location / {
#        root   html;
#        index  index.html index.htm;
#    }
#}


# HTTPS server
#
#server {
#    listen       443 ssl;  //监听443端口，即ssl
#    server_name  localhost;

### 以下为ssl相关配置
#    ssl_certificate      cert.pem;    //指定pem文件路径
#    ssl_certificate_key  cert.key;  //指定key文件路径

#    ssl_session_cache    shared:SSL:1m;  //指定session cache大小
#    ssl_session_timeout  5m;  //指定session超时时间
#    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;   //指定ssl协议
#    ssl_ciphers  HIGH:!aNULL:!MD5;  //指定ssl算法
#    ssl_prefer_server_ciphers  on;  //优先采取服务器算法
#    location / {
#        root   html;
#        index  index.html index.htm;
#    }
#}
```



#### 安装MySql

###### 更新mysql gpg key

```bash
rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
```

官方文档地址 [MySQL ：： MySQL 8.0 参考手册 ：： 2 安装和升级 MySQL](https://dev.mysql.com/doc/refman/8.0/en/installing.html)

<!--Ubuntu跳转第6步-->

###### 1.检查是否已经安装mysql

`rpm -qa|grep mysql`

![image-20221205161315432](image\image-20221205161315432.png)

###### 2.查看mysql当前状态

`service mysqld status`

![image-20221205154545016](C:\Users\jdy2002\AppData\Roaming\Typora\typora-user-images\image-20221205154545016.png)

###### 3.停止mysql服务

`service mysqld stop`

###### 4.卸载mysql

`rpm -ev [需要移除组件的名称]`

还需要移除服务

`yum remove mysql-server`

![image-20221205161558816](image\image-20221205161558816.png)

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

![image-20221205162831710](image\image-20221205162831710.png)

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
