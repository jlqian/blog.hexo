---
title:  "Ubuntu搭建JAVAEE开发环境"
date:   2016-03-13 00:33:37
categories: Java
---
在工作中可能会要求使用Linux系统进行开发，对于不熟悉Linux的人来说，搭建开发环境确实很不容易，写这篇日志的目的主要也是如此。

安装Ubuntu操作系统就不再多说，几乎和Windows差不多了。

搭建开发环境步骤：(注：安装软件均在/usr/local/下)
<!-- more -->
0.设置静态IP与安装GNOME2桌面

0.1设置静态IP

一般情况下，桌面版通过DHCP获取IP就可以了，但是在某些情况下需要设置静态IP，当然可以通过图形界面，对于不太了解Linux的人来讲，容易配置。

图形界面具体配置为：
{% codeblock lang:c %}
IPv4 Settings
Method Manual
Address
Address manual_ip
Netmask manual_mask
Geteway manual_gateway
DNS servers server1 server2
Search domains
Reauire IPv4 addressing for this connection to complete true 
{% endcodeblock %}

命令行配置：在Centos中的nmcli可以创建连接，修改连接，在Ubuntu14.04下，nmcli命令少了很多东西，如connection add – 添加新的连接

对于Ubuntu Server 或者Ubuntu桌面版不想使用NetworkManager的，可以将NetworkManager关闭或卸载（Server版没有安装，Desktop的/etc/init.d/下没有脚本，建议卸载），两者共同存在时，可能会出现问题。

{% codeblock lang:c %}
$sudo apt-get purge network-manager
$sudo apt-get purge network-manager-pptp
$sudo apt-get purge gir1.2-networkmanager-1.0
$sudo apt-get purge libproxy1-plugin-networkmanager
$sudo apt-get automave 
$sudo apt-get clean
{% endcodeblock %}

然后可以按照传统的方式进行配置：

在/etc/network/interfaces文件中增加：
{% codeblock lang:c %}
auto eth0
iface eth0 inet static
address 192.168.1.178
netmask 255.255.255.0
gateway 192.168.1.1
{% endcodeblock %}

在/etc/resolve.conf文件中增加DNS
{% codeblock lang:c %}
nameserver 192.168.1.1
nameserver 192.168.8.10
{% endcodeblock %}

重启网络服务即可：/etc/init.d/networking restart

但是在电脑重启之后，resolve.conf中的内容又会被清空，这是因为resolveconf服务的原因，有两种方式可以解决

第一种方式：在/etc/network/interfaces文件中增加DNS配置
{% codeblock lang:c %}
dns-nameservers 192.168.1.1 192.168.8.10
{% endcodeblock %}

第二种方式：修改 resolvconf服务的配置文件: /etc/resolvconf/resolv.conf.d/base
{% codeblock lang:c %}
nameserver 192.168.1.1
nameserver 192.168.8.10
{% endcodeblock %}

就可以了

0.2安装GOME2桌面

添加GOME源，默认Ubuntu应该带有，然后安装GNOME2
{% codeblock lang:c %}
sudo add-apt-repository ppa:gnome3-team/gnome3 
sudo apt-get update
sudo apt-get install gnome-session-flashback
{% endcodeblock %}

注销，选择GNOME Flashback 登录即可

1.安装openssh-server方便发送文件
{% codeblock lang:c %}
sudo apt-get install openssh-server
{% endcodeblock %}

1.安装openssh-server方便发送文件
{% codeblock lang:c %}
sudo apt-get install openssh-server
{% endcodeblock %}
查看是否启动
{% codeblock lang:c %}
ps -ef | grep sshd
{% endcodeblock %}
如果没有启动，则执行
{% codeblock lang:c %}
sudo service ssh start
{% endcodeblock %}

2.安装vim编辑器，vi编辑器有问题
{% codeblock lang:c %}
sudo apt-get install vim
{% endcodeblock %}

3.安装jdk，以6u45为例
{% codeblock lang:c %}
sudo sh ./jdk-6u45-linux-x64.bin
{% endcodeblock %}
添加JAVA_HOME的环境变量
{% codeblock lang:c %}
vim ~/.profile
{% endcodeblock %}
增加：
{% codeblock lang:c %}
export JAVA_HOME=/usr/local/jdk1.6.0_45
PATH=$JAVA_HOME/bin:$PATH
{% endcodeblock %}
重新加载.profile文件
{% codeblock lang:c %}
source ~/.profile
{% endcodeblock %}
现在就可以使用Java了，java -version打印java的版本信息

4.安装Tomcat，以Tomcat7为例
{% codeblock lang:c %}
sudo tar -zxvf apache-tomcat-7.0.67.tar.gz
{% endcodeblock %}
添加CATALINA_HOME的环境变量
{% codeblock lang:c %}
vim ~/.profile
{% endcodeblock %}
修改：
{% codeblock lang:c %}
export JAVA_HOME=/usr/local/jdk1.6.0_45
export CATALINA_HOME=/usr/local/apache-tomcat-7.0.67
PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$PATH
{% endcodeblock %}
重新加载.profile文件
{% codeblock lang:c %}
source ~/.profile
{% endcodeblock %}
现在就可以使用Tomcat了

5.安装MySQL,以MySQL5为例
{% codeblock lang:c %}
sudo tar -zxvf mysql-5.7.10-linux-glibc2.5-x86_64.tar.gz
sudo ln -s mysql-5.7.10-linux-glibc2.5-x86_64/ mysql 
{% endcodeblock %}
添加MYSQL_HOME的环境变量
{% codeblock lang:c %}
vim ~/.profile
{% endcodeblock %}
修改：
{% codeblock lang:c %}
export JAVA_HOME=/usr/local/jdk1.6.0_45
export CATALINA_HOME=/usr/local/apache-tomcat-7.0.67
export MYSQL_HOME=/usr/local/mysql
PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$MYSQL_HOME/bin:$PATH
{% endcodeblock %}
重新加载.profile文件
{% codeblock lang:c %}
source ~/.profile
{% endcodeblock %}
安装过程完全参考MySQL自带的INSTALL-BINNARY

5.1安装libaio依赖 
{% codeblock lang:c %}
apt-cache search libaio # search for info
sudo apt-get install libaio1 # install library 
{% endcodeblock %}
5.2添加mysql用户和组
{% codeblock lang:c %}
sudo groupadd mysql
sudo useradd -r -g mysql -s /bin/false mysql
{% endcodeblock %}
5.3创建data与mysql-files目录
{% codeblock lang:c %}
cd mysql
sudo mkdir data mysql-files
sudo chmod 770 data/ mysql-files/
{% endcodeblock %}
5.4改变mysql目录的用户和组
{% codeblock lang:c %}
sudo chown -R mysql .
sudo chgrp -R mysql .
{% endcodeblock %}
5.5初始化数据库，会生成root密码
{% codeblock lang:c %}
sudo bin/mysqld --initialize --user=mysql    #记住root密码
sudo bin/mysql_ssl_rsa_setup
sudo chown -R root .
sudo chown -R mysql data mysql-files
sudo bin/mysqld_safe --user=mysql &
{% endcodeblock %}
5.6加入Mysql服务
{% codeblock lang:c %}
sudo cp support-files/mysql.server /etc/init.d/mysql.server
{% endcodeblock %}
5.7修改root密码

使用生成的root密码登录后任何操作都会提示：You must reset your password using ALTER USER statement before executing this statement.
{% codeblock lang:c %}
mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
{% endcodeblock %}
如果忘记root密码，可以--skip-grant-tables的启动方式进行修改
{% codeblock lang:c %}
sudo service mysql.server stop
cd /usr/local/mysql/bin/
sudo ./mysqld_safe --skip-grant-tables
mysql  #另开shell
mysql> use mysql;
mysql> UPDATE user SET authentication_string=PASSWORD('1234567') WHERE user='root';
mysql> FLUSH PRIVILEGES;
mysql> exit
sudo service mysql.server restart
{% endcodeblock %}

6.安装NGINX
{% codeblock lang:c %}
sudo tar -zxvf nginx-1.9.9.tar.gz
{% endcodeblock %}
6.1安装依赖:(pcre，zlib)
{% codeblock lang:c %}
sudo apt-get install libpcre3-dev #也可以到PCRE官网下载进行安装
sudo sudo apt-get install zlib1g-dev
{% endcodeblock %}
6.2编译安装
{% codeblock lang:c %}
sudo ./configure --prefix=/usr/local/nginx
sudo make
sudo make install
{% endcodeblock %}
6.3添加NGINX_HOME的环境变量
{% codeblock lang:c %}
vim ~/.profile
{% endcodeblock %}
增加：
{% codeblock lang:c %}
export JAVA_HOME=/usr/local/jdk1.6.0_45
export CATALINA_HOME=/usr/local/apache-tomcat-7.0.67
export MYSQL_HOME=/usr/local/mysql
export NGINX_HOME=/usr/local/nginx
PATH=$JAVA_HOME/bin:$CATALINA_HOME/bin:$MYSQL_HOME/bin:$NGINX_HOME/sbin:$PATH
{% endcodeblock %}
重新加载.profile文件
{% codeblock lang:c %}
source ~/.profile
{% endcodeblock %}

7.安装Eclipse
{% codeblock lang:c %}
sudo tar -zxvf eclipse-jee-luna-SR2-linux-gtk-x86_64.tar.gz
{% endcodeblock %}
创建桌面快捷方式
{% codeblock lang:c %}
sudo vim /usr/share/applications/eclipse.desktop
{% endcodeblock %}
内容为：
{% codeblock lang:c %}
#!/usr/bin/env xdg-open
[Desktop Entry]
Name=Eclipse
Comment=Eclipse IDE
Keywords=documentation;information;manual;
OnlyShowIn=GNOME;Unity;
Exec=/usr/local/eclipse/eclipse
Icon=/usr/local/eclipse/icon.xpm
StartupNotify=true
Terminal=false
Type=Application
Categories=GNOME;GTK;Core;Documentation;Utility;
{% endcodeblock %}
复制一份到桌面是上即可

8.安装Lantern
{% codeblock lang:c %}
sudo dpkg -i lantern-installer-beta-64-bit.deb
{% endcodeblock %}
复制快捷方式
{% codeblock lang:c %}
cp /usr/share/applications/lantern.desktop ~/Desktop/
chmod +x ~/Desktop/lantern.desktop 
{% endcodeblock %}

9.安装搜狗输入法
{% codeblock lang:c %}
sudo add-apt-repository ppa:fcitx-team/nightly
sudo apt-get update
sudo apt-get install fcitx
sudo dpkg -i sogoupinyin_2.0.0.0068_amd64.deb 
{% endcodeblock %}
打开Language Support提示没有安装完成，直接Install就行了，如果没有sogou拼音，打开Input Method Configuration-->Add Input Method-->Only Show Current Language 去掉，就会显示sogoupinyin，添加就可以了。

10.安装adobe flash player
{% codeblock lang:c %}
sudo tar -zxvf install_flash_player_11_linux.x86_64.tar.gz 
sudo cp libflashplayer.so /usr/lib/mozilla/plugins/
sudo chmod 755 /usr/lib/mozilla/plugins/libflashplayer.so
{% endcodeblock %}

[本文中的所有软件均可以在百度网盘下载][baiduyun]
[附上本人的GitHub地址][github]

[github]: https://github.com/jlqian
[baiduyun]: http://pan.baidu.com/s/1pKpWv6j