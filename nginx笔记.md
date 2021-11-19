# nginx笔记

## Nginx 安装

系统平台：CentOS release 6.6 (Final) 64位。

### 一、安装编译工具及库文件

```
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```

### 二、首先要安装 PCRE

PCRE 作用是让 Nginx 支持 Rewrite 功能。

1、下载 PCRE 安装包，下载地址： http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz

```
[root@bogon src]# cd /usr/local/src/
[root@bogon src]# wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
```

![img](.\picture\nginx1.png)

2、解压安装包:

```
[root@bogon src]# tar zxvf pcre-8.35.tar.gz
```

3、进入安装包目录

```
[root@bogon src]# cd pcre-8.35
```

4、编译安装 

```
[root@bogon pcre-8.35]# ./configure
[root@bogon pcre-8.35]# make && make install
```

5、查看pcre版本

```
[root@bogon pcre-8.35]# pcre-config --version
```

![img](.\picture\nginx2.png)

### 安装 Nginx

1、下载 Nginx，下载地址：https://nginx.org/en/download.html

```
[root@bogon src]# cd /usr/local/src/
[root@bogon src]# wget http://nginx.org/download/nginx-1.6.2.tar.gz
```

![img](.\picture\nginx3.png) 2、解压安装包

```
[root@bogon src]# tar zxvf nginx-1.6.2.tar.gz
```

3、进入安装包目录

```
[root@bogon src]# cd nginx-1.6.2
```

4、编译安装

```
[root@bogon nginx-1.6.2]# ./configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.35
[root@bogon nginx-1.6.2]# make
[root@bogon nginx-1.6.2]# make install
```

5、查看nginx版本

```
[root@bogon nginx-1.6.2]# /usr/local/webserver/nginx/sbin/nginx -v
```

![img](.\picture\nginx4.png)

到此，nginx安装完成。

------

## Nginx 配置

创建 Nginx 运行使用的用户 www：

```
[root@bogon conf]# /usr/sbin/groupadd www 
[root@bogon conf]# /usr/sbin/useradd -g www www
```

配置nginx.conf ，将/usr/local/webserver/nginx/conf/nginx.conf替换为以下内容



```
[root@bogon conf]#  cat /usr/local/webserver/nginx/conf/nginx.conf

user www www;
worker_processes 2; #设置值和CPU核心数一致
error_log /usr/local/webserver/nginx/logs/nginx_error.log crit; #日志位置和日志级别
pid /usr/local/webserver/nginx/nginx.pid;
#Specifies the value for maximum file descriptors that can be opened by this process.
worker_rlimit_nofile 65535;
events
{
  use epoll;
  worker_connections 65535;
}
http
{
  include mime.types;
  default_type application/octet-stream;
  log_format main  '$remote_addr - $remote_user [$time_local] "$request" '
               '$status $body_bytes_sent "$http_referer" '
               '"$http_user_agent" $http_x_forwarded_for';
  
#charset gb2312;
     
  server_names_hash_bucket_size 128;
  client_header_buffer_size 32k;
  large_client_header_buffers 4 32k;
  client_max_body_size 8m;
     
  sendfile on;
  tcp_nopush on;
  keepalive_timeout 60;
  tcp_nodelay on;
  fastcgi_connect_timeout 300;
  fastcgi_send_timeout 300;
  fastcgi_read_timeout 300;
  fastcgi_buffer_size 64k;
  fastcgi_buffers 4 64k;
  fastcgi_busy_buffers_size 128k;
  fastcgi_temp_file_write_size 128k;
  gzip on; 
  gzip_min_length 1k;
  gzip_buffers 4 16k;
  gzip_http_version 1.0;
  gzip_comp_level 2;
  gzip_types text/plain application/x-javascript text/css application/xml;
  gzip_vary on;
 
  #limit_zone crawler $binary_remote_addr 10m;
 #下面是server虚拟主机的配置
 server
  {
    listen 80;#监听端口
    server_name localhost;#域名
    index index.html index.htm index.php;
    root /usr/local/webserver/nginx/html;#站点目录
      location ~ .*\.(php|php5)?$
    {
      #fastcgi_pass unix:/tmp/php-cgi.sock;
      fastcgi_pass 127.0.0.1:9000;
      fastcgi_index index.php;
      include fastcgi.conf;
    }
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$
    {
      expires 30d;
  # access_log off;
    }
    location ~ .*\.(js|css)?$
    {
      expires 15d;
   # access_log off;
    }
    access_log off;
  }

}
```

检查配置文件nginx.conf的正确性命令：

```
[root@bogon conf]# /usr/local/webserver/nginx/sbin/nginx -t
```

![img](.\picture\nginx5.png)

------

## 启动 Nginx

Nginx 启动命令如下：

```
[root@bogon conf]# /usr/local/webserver/nginx/sbin/nginx
```

![img](.\picture\nginx6.png)

------

## 访问站点

从浏览器访问我们配置的站点ip：

![img](.\picture\nginx7.png)

------

## Nginx 其他命令

以下包含了 Nginx 常用的几个命令：

```
/usr/local/webserver/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/webserver/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/webserver/nginx/sbin/nginx -s stop              # 停止 Nginx
```



### nginx并发连接能力优化

1. 改进方法

```
a、使用epoll模式，增加并发连接数，增加nginx系统并发连接能力。``b、后端使用长连接, 提高端口利用率，减少TIME_WAIT状态比例, 使系统可以允许更多的TIME_WAIT.``c、优化tcp连接工作模式，减少FIN2_WAIT状态比例 (对应监控中的OTHERSTATE)。``d、合理的设置监控参数
```

2. 配置操作

   2.1 打开nginx/conf/nginx.conf配置文件，对其中events的worker_connections、multi_accept、accept_mutex等参进行调优，如下所示

原配置

```
events {
	worker_connections 1024;
}
```

优化为

```
events {
    use epoll; #支持大量连接和非活动连接
    worker_connections 32768;
    multi_accept on; #nginx在已经得到一个新连接的通知时，接收尽可能多的连接
    accept_mutex on; #防止惊群现象发生，默认为on``
}
```

优化说明

```
使用epoll模式，将连接从现在的2048增加到32768，Epoll模式将提高并发连接到100K级别, 而且非活跃的连接（连接正常但没有数据或死的连接对象）数量不影响活跃连接的性能。
```

2.2 upstream中使用keepalive，如下

```
upstream httpproxy{
    server 192.168.1.14:8080;
    server 192.168.1.15:8080;
    keepalive 128; 
}
```

2.3 系统优化

```
[root@CsadfZ999739 nginx]# vi /etc/sysctl.conf 
net.ipv4.tcp_fin_timeout = 20
#net.ipv4.tcp_keepalive_time =300 # 5分钟发个激活包，应对移动端, 现在不需要，因为有F5
fs.file``-max = 163840 ``#增加文件数
net.ipv4.tcp_max_syn_backlog = 8192 ``#表示SYN队列的长度，默认为1024，加大队列长度为8192，可以容纳更多等待连接的网络连接数。 
:wq 
[root@CsadfZ999739 nginx]#/sbin/sysctl
#监控参数设置
ESTABLISHED: 连接数 2万 
TIME_WAIT: 只监控nginx与后端服务器连接部分 5000 全部的端口>2万
OTHERSTATE: 5000
```