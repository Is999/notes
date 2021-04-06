# 一、centos7 静态网络配置

## 第一步, 配置文件
```bash
cd /etc/sysconfig/network-scripts/
vi ifcfg-ens33
```



### 以下为配置文件
```bash
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=static # none 改为static
```



### 添加以下内容
```bash
IPADDR=192.168.3.50 #地址
NETMASK=255.255.255.0 #子网掩码
GATEWAY=192.168.3.253 #默认网关
DNS1=114.114.114.114 #DNS

DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=2e5b0318-d59b-41b7-8de0-b70572f2663a
DEVICE=ens33
ONBOOT=yes
```



## 第二步, 重启服务

```
systemctl restart network

systemctl status network
systemctl start network
systemctl stop network
```



# 二、安装服务 

## 1. 安装vim
```bash
yum -y install vim*
```



## 2. 安装wget
```bash
yum -y install wget
```



## 3. 安装lnmpa

### 第一步, 下载lnmpa1.6
https://lnmp.org/faq/lnmp-status-manager.html

https://lnmp.org/auto.html 无人值守自动安装生成命令

```bash
wget http://soft.vpser.net/lnmp/lnmp1.6.tar.gz -cO lnmp1.6.tar.gz && tar zxf lnmp1.6.tar.gz && cd lnmp1.6 && LNMP_Auto="y" DBSelect="5" DB_Root_Password="root" InstallInnodb="y" PHPSelect="9" SelectMalloc="2" ApacheSelect="2" ServerAdmin="909931038@qq.com CheckMirror="n"" ./install.sh lnmpa
```



### 第二步, 安装 redis memcached
https://lnmp.org/faq/addons.html

```bash
./addons.sh install redis
./addons.sh install memcached
```

卸载:

```bash
./addons.sh uninstall memcached
```



### 第三步, 配置mysql, 添加用户,和权限
https://www.cnblogs.com/sos-blue/p/6852945.html

```bash
mysql -uroot -proot # 进入mysql 控制台
CREATE USER 'rui'@'%' IDENTIFIED BY 'rui'; #创建用户
GRANT ALL ON *.* TO 'rui'@'%'; #授权
```

其它方法: 用phpmyadmin 创建

http://192.168.3.72/phpmyadmin/



### 第四步, 配置php
```bash
vim /usr/local/php/etc/php.ini
```

1. Symfony 依赖的方法, 去掉禁用的方法: proc_open
2. composer 安装报错, 去掉禁用的方法: proc_get_status
; disable_functions = passthru,exec,system,chroot,chgrp,chown,shell_exec,proc_open,proc_get_status,popen,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server
disable_functions = passthru,exec,system,chroot,chgrp,chown,shell_exec,popen,ini_alter,ini_restore,dl,openlog,syslog,readlink,symlink,popepassthru,stream_socket_server



### 第五步, 配置虚拟主机
https://lnmp.org/faq/lnmp-vhost-add-howto.html

虚拟主机管理基本命令介绍：lnmp vhost {add|list|del}

#### apache vhost 配置

```bash
lnmp vhost add

cd /usr/local/apache/conf/vhost/

vim xxx.conf
```

```ini
#php_admin_value open_basedir "home/wwwroot/laravel/Binary/cccwz/public:/tmp/:/var/tmp/:/proc/"

php_admin_value open_basedir "home/wwwroot/laravel/Binary/cccwz/public:/tmp/:/var/tmp/:/proc/:/../" #追加 :/../  防跨目录设置
```



#### nginx vhost 配置

```bash
cd /usr/local/nginx/conf/vhost

vim xxx.conf
```

```nginx
server
    {
        listen 80;
        #listen [::]:80;
        server_name www.binary.lp ;
        index index.html index.htm index.php default.html default.htm default.php;
        root  /home/wwwroot/laravel/Binary/cccwz/public;

​    #增加
​    charset utf-8;
​    add_header X-Frame-Options "SAMEORIGIN";
​    add_header X-XSS-Protection "1; mode=block";
​    add_header X-Content-Type-Options "nosniff";
​    #增加end

​    #error_page   404   /404.html;

    # Deny access to PHP files in specific directory

​    #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }

​    include proxy-pass-php.conf;

​    #增加
​    include enable-php-pathinfo.conf;

​    location ~ \.php$ {
​        fastcgi_index index.php;
​        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
​        include fastcgi_params;
​    }
​    #增加end
​	
​    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
​    {
​        expires      30d;
​    }

​    location ~ .*\.(js|css)?$
​    {
​        expires      12h;
​    }

​    location ~ /.well-known {
​        allow all;
​    }

​    location ~ /\.
​    {
​        deny all;
​    }

​    access_log  /home/wwwlogs/www.binary.lp.log;
}


```



### 第六步, 入口文件目录添加.htaccess 文件(找不到上级目录,添加该文件)



# 三、VMware 设置win与linux 共享文件

1. VMware 设置共享的文件目录/文件
2. mkdir /mnt/hgfs 创建目录hgfs
3. /usr/bin/vmhgfs-fuse .host:/ /mnt/hgfs -o subtype=vmhgfs-fuse,allow_other  执行该命令就可以看到共享的目录
    以上都不行请执行以下命令:
    1. yum install open-vm-tools-desktop
    
    2. yum install perl gcc kernel-headers kernel-devel
    
    3. 安装 VMware tools
       3.1 mkdir /mnt/cdrom  创建cdrom
       3.2 mount /dev/cdrom /mnt/cdrom 挂载
       3.3 tar zxpf /mnt/cdrom/VMwareTools-x.x.x-yyyy.tar.gz 先查看/mnt/cdrom 目录下是否有 tar zxpf /mnt/cdrom/VMwareTools-x.x.x-yyyy.tar.gz 文件, 再解压
       3.4 cd vmware-tools-distrib && sudo ./vmware-install.pl 进入目录,并执行安装命令
       3.5 umount /dev/cdrom 卸载
       
       

# 四、lnmpa 状态管理

https://lnmp.org/faq/lnmp-status-manager.html

LNMPA 1.2+状态管理: lnmp {start|stop|reload|restart|kill|status}
LNMPA 1.2+各个程序状态管理: lnmp {httpd|mysql|mariadb|pureftpd} {start|stop|reload|restart|kill|status}
LNMPA1.1状态管理： /root/lnmpa {start|stop|reload|restart|kill|status}
Nginx状态管理：/etc/init.d/nginx {start|stop|reload|restart}
MySQL状态管理：/etc/init.d/mysql {start|stop|restart|reload|force-reload|status}
Memcached状态管理：/etc/init.d/memcached {start|stop|restart}
PureFTPd状态管理： /etc/init.d/pureftpd {start|stop|restart|kill|status}
ProFTPd状态管理： /etc/init.d/proftpd {start|stop|restart|reload}

Apache状态管理：/etc/init.d/httpd {start|stop|restart|graceful|graceful-stop|configtest|status}

