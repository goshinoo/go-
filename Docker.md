



# Docker

## 底层原理

### **Docker是如何工作的？**

Docker是一个CS结构的系统，以守护进程运行在主机上，通过Socket从客户端访问。

DockerServer接收到client的指令就会执行

### Docker为什么比VM快?

1.Docker有更少的抽象层

2.Docker利用的是宿主机的内核



## 常用命令

### 帮助命令

#### docker version #查看版本

#### docker info #查看信息

#### docker 命令 --help #查看命令详情



### 镜像命令

#### docekr images #查看本地镜像

```shell
C:\Users\admin>docker images
REPOSITORY                TAG       IMAGE ID       CREATED        SIZE
loginserver_loginserver   latest    a416606dba53   19 hours ago   1.03GB
ubuntu                    latest    54c9d81cbb44   9 days ago     72.8MB
golang                    latest    80d9a75ccb38   2 weeks ago    941MB
hello-world               latest    feb5d9fea6a5   4 months ago   13.3kB

-a,--all #列出所有镜像
-q,--quiet #只显示镜像ID
```



#### docker search #搜索镜像

```shell
C:\Users\admin>docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   12096     [OK]
mariadb                           MariaDB Server is a high performing open sou…   4634      [OK]
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   905                  [OK]
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   92
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   92
centurylink/mysql                 Image containing mysql. Optimized to be link…   59                   [OK]
databack/mysql-backup             Back up mysql databases to... anywhere!         54
prom/mysqld-exporter                                                              46                   [OK]
deitch/mysql-backup               REPLACED! Please use http://hub.docker.com/r…   40                   [OK]
linuxserver/mysql                 A Mysql container, brought to you by LinuxSe…   35
tutum/mysql                       Base docker image to run a MySQL database se…   35
schickling/mysql-backup-s3        Backup MySQL to S3 (supports periodic backup…   31                   [OK]
mysql/mysql-router                MySQL Router provides transparent routing be…   23
centos/mysql-56-centos7           MySQL 5.6 SQL database server                   21
arey/mysql-client                 Run a MySQL client from a docker container      20                   [OK]
fradelg/mysql-cron-backup         MySQL/MariaDB database backup using cron tas…   18                   [OK]
genschsa/mysql-employees          MySQL Employee Sample Database                  9                    [OK]
yloeffler/mysql-backup            This image runs mysqldump to backup data usi…   7                    [OK]
openshift/mysql-55-centos7        DEPRECATED: A Centos7 based MySQL v5.5 image…   6
idoall/mysql                      MySQL is a widely used, open-source relation…   3                    [OK]
ansibleplaybookbundle/mysql-apb   An APB which deploys RHSCL MySQL                3                    [OK]
devilbox/mysql                    Retagged MySQL, MariaDB and PerconaDB offici…   3
jelastic/mysql                    An image of the MySQL database server mainta…   2
centos/mysql-80-centos7           MySQL 8.0 SQL database server                   2
widdpim/mysql-client              Dockerized MySQL Client (5.7) including Curl…   1                    [OK]


-filter=STARS=3000 #只搜索stars大于3000的
```



#### docker pull #下载镜像

```shell
C:\Users\admin>docker pull mysql
Using default tag: latest #如果不写tag,默认是latest
latest: Pulling from library/mysql
6552179c3509: Pull complete #分层下载.docker image核心 联合文件系统
d69aa66e4482: Pull complete
3b19465b002b: Pull complete
7b0d0cfe99a1: Pull complete
9ccd5a5c8987: Pull complete
2dab00d7d232: Pull complete
64d3afdccd4a: Pull complete
82148d50b16c: Pull complete
8bb7d73a7d0c: Pull complete
74778cd68a75: Pull complete
d7e5f9309140: Pull complete
f2e376ecd59f: Pull complete
Digest: sha256:92d27b8222bbcf53bc42c70ca7cd1010d6c0527efc61f14980ce77c50932bef4 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest #真实地址

#等价
docker pull mysql
docker pull docker.io/library/mysql:latest

#指定版本下载
C:\Users\admin>docker pull mysql:5.7
5.7: Pulling from library/mysql
6552179c3509: Already exists #重复的层级不再下载
d69aa66e4482: Already exists
3b19465b002b: Already exists
7b0d0cfe99a1: Already exists
9ccd5a5c8987: Already exists
2dab00d7d232: Already exists
64d3afdccd4a: Already exists
6992e58be0f2: Pull complete
67313986b81d: Pull complete
7c36a23db0a4: Pull complete
d34c396e3198: Pull complete
Digest: sha256:afc453de0d675083ac00d0538521f8a9a67d1cce180d70fab9925ebcc87a0eba
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```



#### docker rmi #删除镜像

```shell
docekr rmi -f 容器ID #删除指定容器
docker rmi -f 容器ID 容器ID 容器ID #删除多个容器
docker rmi -f $(docker images -aq) #删除全部的容器
```



#### docker image #管理镜像

```shell
build       Build an image from a Dockerfile
history     Show the history of an image
import      Import the contents from a tarball to create a filesystem image
inspect     Display detailed information on one or more images
load        Load an image from a tar archive or STDIN
ls          List images
prune       Remove unused images
pull        Pull an image or a repository from a registry
push        Push an image or a repository to a registry
rm          Remove one or more images
save        Save one or more images to a tar archive (streamed to STDOUT by default)
tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
```



### 容器命令

有了镜像才能创建容器

#### docker run  #新建容器并启动

```shell
--name="Name" #容器名字
-d #后台方式运行
-it #i使用交互方式运行,t进入容器查看内容
-p #指定容器端口
	-p 主机端口:容器端口
	-p 容器端口	

	
C:\Users\admin>docker run -it centos bash
[root@e0f05a501d45 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

#退出容器
[root@e0f05a501d45 /]# exit
exit
#或 容器不会停止
Ctrl + p + q
```



#### docker ps #列出容器

```shell
-a #所有容器
-n=? #最近创建的容器
-q #只显示容器编号

C:\Users\admin>docker ps -a
CONTAINER ID   IMAGE                     COMMAND                  CREATED         STATUS                     PORTS                                                                    NAMES
e0f05a501d45   centos                    "bash"                   5 minutes ago   Exited (0) 4 minutes ago                                                                            nifty_albattani
f244836c536f   ubuntu:latest             "bash"                   2 hours ago     Exited (255) 2 hours ago                                                                            ubuntu
457e570f45fd   loginserver_loginserver   "go run /go/workspac…"   5 hours ago     Up 2 hours                 0.0.0.0:6060->6060/tcp, 0.0.0.0:8099->8099/tcp, 0.0.0.0:9090->9090/tcp   457e570f45fd_loginserver_loginserver_1
8c18e3e0db52   ubuntu:latest             "bash"                   23 hours ago    Exited (255) 2 hours ago                                                                            wonderful_sutherland

```



#### docker rm #删除容器

```shell
-f #强制删除

#删除全部容器
docker rm -f $(docker ps -aq)
docker ps -a -q | xargs docker rm
```

#### docker start #启动

#### docker restart #重启

#### docker stop #停止

#### docker kill #强制停止

#### docker logs #查看日志

```shell
-t #显示时间
-f #实时显示log
--tail number #显示最后几行
```

#### docker top #查看进程

#### docker inspect #查看容器信息

```shell
C:\Users\admin>docker inspect 457e570f45fd
[
    {
        "Id": "457e570f45fd9e79f5ec6a4ae202e117e06ff55b2990b2742a7b9d6b001fc92b",
        "Created": "2022-02-11T02:36:06.5312028Z",
        "Path": "go",
        "Args": [
            "run",
            "/go/workspace/loginserver/main.go"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 1339,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-02-11T06:07:43.1681176Z",
            "FinishedAt": "2022-02-11T06:07:20.1703908Z"
        },
        "Image": "sha256:a416606dba53af90accb1f578aa1c56134c45ed57cd00b4301b859ba907c8254",
        "ResolvConfPath": "/var/lib/docker/containers/457e570f45fd9e79f5ec6a4ae202e117e06ff55b2990b2742a7b9d6b001fc92b/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/457e570f45fd9e79f5ec6a4ae202e117e06ff55b2990b2742a7b9d6b001fc92b/hostname",
        "HostsPath": "/var/lib/docker/containers/457e570f45fd9e79f5ec6a4ae202e117e06ff55b2990b2742a7b9d6b001fc92b/hosts",
        "LogPath": "/var/lib/docker/containers/457e570f45fd9e79f5ec6a4ae202e117e06ff55b2990b2742a7b9d6b001fc92b/457e570f45fd9e79f5ec6a4ae202e117e06ff55b2990b2742a7b9d6b001fc92b-json.log",
        "Name": "/457e570f45fd_loginserver_loginserver_1",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": [
                "D:\\go\\workspace\\loginserver:/go/workspace/loginserver:rw"
            ],
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "loginserver_default",
            "PortBindings": {
                "6060/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "6060"
                    }
                ],
                "8099/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "8099"
                    }
                ],
                "9090/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "9090"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": [],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": null,
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": null,
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/1bc4cf9c6b20a492c3ed6d1c6a50ebcfbed3ee138b58bd0abcbd8ed93a44c7c1-init/diff:/var/lib/docker/overlay2/s72icivlmsxzitz1iz7ddlwvd/diff:/var/lib/docker/overlay2/xwadgo5p3q534hjuqtq5qe38f/diff:/var/lib/docker/overlay2/16cfc175c83ced43eb9619e2c7008975e28b859447d6ee17aa1acb7b87f6afb6/diff:/var/lib/docker/overlay2/66ca13b659c9e4356cbc7197b56cacd5ee07aae7050ea52de2306ba28b15a44e/diff:/var/lib/docker/overlay2/409691d091719ae7fa55e8181619961468bdd274faaa5c75d11f4d60a817d400/diff:/var/lib/docker/overlay2/af1a6cb166d7ebdc5ca8d6262dbe1a15cb296517f4456adceca25bf3b3414b99/diff:/var/lib/docker/overlay2/46c1274062d7b2e64d050648f96170eb78129057e5f8abecc9c61c20c37a3339/diff:/var/lib/docker/overlay2/41950f7e86fbf400ae779e01463df5f307bbb6bd0a54c2b70a8a19ec24f96765/diff:/var/lib/docker/overlay2/f67c65a6707174a0c80bf103fb0709fb0a5764a099b9f71de4e91851177a5f26/diff",
                "MergedDir": "/var/lib/docker/overlay2/1bc4cf9c6b20a492c3ed6d1c6a50ebcfbed3ee138b58bd0abcbd8ed93a44c7c1/merged",
                "UpperDir": "/var/lib/docker/overlay2/1bc4cf9c6b20a492c3ed6d1c6a50ebcfbed3ee138b58bd0abcbd8ed93a44c7c1/diff",
                "WorkDir": "/var/lib/docker/overlay2/1bc4cf9c6b20a492c3ed6d1c6a50ebcfbed3ee138b58bd0abcbd8ed93a44c7c1/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [
            {
                "Type": "bind",
                "Source": "D:\\go\\workspace\\loginserver",
                "Destination": "/go/workspace/loginserver",
                "Mode": "rw",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
        "Config": {
            "Hostname": "457e570f45fd",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6060/tcp": {},
                "8099/tcp": {},
                "9090/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/go/bin:/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOLANG_VERSION=1.17.6",
                "GOPATH=/go"
            ],
            "Cmd": [
                "go",
                "run",
                "/go/workspace/loginserver/main.go"
            ],
            "Image": "loginserver_loginserver",
            "Volumes": {
                "/go/workspace/loginserver": {}
            },
            "WorkingDir": "/go/workspace/loginserver",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "com.docker.compose.config-hash": "313e2ab98d1728b6e74a0e3145c2da297354b9b5c5e554b1a26d174ab8ede355",
                "com.docker.compose.container-number": "1",
                "com.docker.compose.oneoff": "False",
                "com.docker.compose.project": "loginserver",
                "com.docker.compose.project.config_files": "D:\\go\\workspace\\loginserver\\docker-compose.yml",
                "com.docker.compose.project.working_dir": "D:\\go\\workspace\\loginserver",
                "com.docker.compose.service": "loginserver",
                "com.docker.compose.version": "1.29.2"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "2641649f192f36860f212a6431a12b7e484a7aefae9561294d08ec43e88b275d",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "6060/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "6060"
                    }
                ],
                "8099/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "8099"
                    }
                ],
                "9090/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "9090"
                    }
                ]
            },
            "SandboxKey": "/var/run/docker/netns/2641649f192f",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "loginserver_default": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": [
                        "457e570f45fd",
                        "loginserver"
                    ],
                    "NetworkID": "f66ae93338aa9230197ec76e7f4eb7595313a6db4da4ec89c4eb360021597c14",
                    "EndpointID": "61bfb7db6000a7f55d38308ad8cebb6e42f6db133c59d76eddfb04f1a06bf21d",
                    "Gateway": "172.22.0.1",
                    "IPAddress": "172.22.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:16:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```



#### docker exec/docker attach #进入当前正在运行的容器

```shell
C:\Users\admin>docker exec -it 457e570f45fd bash
root@457e570f45fd:/go/workspace/loginserver#

docker exec #进入容器后开启一个新的终端,可以在里边操作
docker attach #进入容器正在执行的终端,不会启动新进程
```



#### docker cp #从容器内拷贝文件到主机上

```shell
docker cp 457e570f45fd:/root/aaa.txt D:/

docker cp 容器ID:容器文件地址 主机地址
```



#### docker stats #查看容器占用情况

```shell
CONTAINER ID   NAME                        CPU %     MEM USAGE / LIMIT     MEM %     NET I/O          BLOCK I/O   PIDS
d22ffc21b69a   loginserver_loginserver_1   0.07%     152.8MiB / 25.01GiB   0.60%     20.3MB / 644kB   0B / 0B     46
```



## 可视化工具

### portainer

```shell
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

![image-20220211174745503](picture\image-20220211174745503.png)



## 镜像讲解

### 镜像是什么

镜像是一种轻量级,可执行的独立软件包,用来打包软件运行环境和基于运行环境开发的软件,它包含某个软件所需的所有内容,包括代码,运行时,库,环境变量和配置文件.

所有的应用,打包成docker镜像,就可以直接跑起来.

如何得到镜像:

- 从远程仓库下载
- 朋友拷贝
- 自己制作镜像Dockerfile

### Docker镜像加载原理

#### UnionFS(联合文件系统)

一种分层,轻量级并且高性能的文件系统,支持对文件系统的修改作为一次提交来一层层叠加,同时可以将不同目录挂载到同一个虚拟文件系统下.

镜像可以通过分层来继承,基于基础镜像,可以制作各种具体镜像.

**特性:**一次同时加载多个文件系统,但从外面看起来只能看到一个文件系统,联合加载会把各层文件系统叠加起来,这样最总的文件系统会包含所有的底层文件和目录.

简单来说就是多个镜像如果有相同层,则可以共用这些相同层的文件,避免多次下载.

![image-20220211181042398](picture\image-20220211181042398.png)

#### 加载原理

docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

bootfs(boot file system)主要包含bootloader和kernel,bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs (root file system)，在bootfs之上。包含的就是典型Linux系统中的/dev,/proc, /bin, /etc等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu ,Centos等等。

![image-20220211180456314](picture\image-20220211180456314.png)

对于一个精简的OS， rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接用Host的kernel ,自己只需要提供rootfs就可以了。

由此可见对于不同的linux发行版, bootfs基本是一致的, rootfs会有差别,因此不同的发行版可以公用bootfs。

虚拟机是分钟级别，容器是秒级! 

### 特点

Docker镜像都是只读的,当容器启动时,一个新的可写层被加载到镜像的顶部

这层就是我们通常说的容器层,容器之下的都叫镜像层!

![image-20220211182602849](picture\image-20220211182602849.png)



### commit镜像

```shell
docker commit 提交容器成为一个新的副本

-m 描述信息
-a 作者

C:\Users\admin>docker commit -m loginserver -a gyj d22ffc21b69a loginserver:v1
sha256:8a7dbbc785e2c49d4ac9c65b676c5c6496c59febcef734f9367e5dbc6604c3de
```



## 容器数据卷

如果数据都在容器中，那么如果容器删除，数据就会丢失，需求：数据可以持久化

容器之间可以有一个数据共享的技术，Docker容器中产生的数据同步到本地

这就是卷技术，目录的挂载

总结：容器的持久化和同步操作，容器间也是可以数据共享的

### 使用数据卷

```shell
docker run -it -v 主机目录:容器目录

C:\Users\gyj>docker run -d -v D:/volumnTest:/home redis
0ca7c0168f692f4ca262effe0de270b100687881e600245b27890ae31b0f1224

#docker inspect 
#挂载成功
```

![image-20220213144821297](picture\image-20220213144821297.png)

测试文件同步成功：

![image-20220213145150833](picture\image-20220213145150833.png)

就算关闭容器后，修改宿主机文件，再开容器依旧同步



### 实战：mysql配置

```shell
C:\Users\gyj>docker run -d -p 3310:3306 -v D:/volumnTest/mysql/conf:/etc/mysql/conf.d -v D:/volumnTest/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql
b6f5154a65a402bd39a231770c06bc42ebdebf089c016e5ce1600e45238ec6ac

-e #配置环境
```

![image-20220213154206261](picture\image-20220213154206261.png)

新建test数据库

![image-20220213154345186](picture\image-20220213154345186.png)

删除容器后数据也没有消失！



### 具名和匿名挂载

```shell
#匿名挂载
-v 容器内路径
docker run -d -P --name nginx01 -v /etc/nginx nginx

#查看所有volume的情况
docker volume ls

#具名挂载
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx

C:\Users\gyj>docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2022-02-13T07:57:00Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

```shell
#匿名挂载
-v 容器内路径
#具名挂载
-v 卷名:容器内路径
#指定路径挂载
-v 宿主机路径:容器内路径
```

扩展：

```shell
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

ro: read-only 只读 一旦设置了容器权限，容器就无法修改文件，只能宿主机修改
rw: read-write 可读可写
```



### 初识Dockerfile

Dockerfile就是用来构建docker镜像的构建文件!命令脚本!

通过这个脚本可以生成镜像,脚本的每个命令都是一层!

```shell
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "----end----"

CMD /bin/bash
```

```shell
sudo docker build -f Dockerfile -t centostest .

Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
latest: Pulling from library/centos
a1d0c7532777: Pull complete
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
 ---> 5d0da3dc9764
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in 37c6907bc35c
Removing intermediate container 37c6907bc35c
 ---> 0eabfafd160d
Step 3/4 : CMD echo "----end----"
 ---> Running in 58d9540a89c2
Removing intermediate container 58d9540a89c2
 ---> da44a2b9b0a8
Step 4/4 : CMD /bin/bash
 ---> Running in 0ba124cd014e
Removing intermediate container 0ba124cd014e
 ---> de8f1801ccd7
Successfully built de8f1801ccd7
Successfully tagged centostest:latest
```

这个目录就是生成镜像自动挂载的:

![image-20220214144948728](picture\image-20220214144948728.png)

自动挂载地址:

![image-20220214150137102](picture\image-20220214150137102.png)

挂载地址可以查找到容器中创建的文件:

test.txt为容器内创建

![image-20220214145911374](picture\image-20220214145911374.png)





### 数据卷容器

多个mysql同步数据

![image-20220214150524487](picture\image-20220214150524487.png)

```shell
#docker1是上文创建的容器,挂载了volume01和volume02

#docker02挂载到docker1
root@DESKTOP-P6DO84T:~# docker run -it -d --name docker02 --volumes-from docker1 de8f1801ccd7
53b851753e9b3d875ca04c094487f0e85175d6ac256ce205e646a1f2e50005da

--volumes-from #容器间数据共享
```



```shell
#测试:可以删除docker1,查看docker02是否还可以访问这个文件

#结果:依旧可以访问,因为宿主机的地址还在,容器的卷指向的宿主机地址是同一个
```

**结论:**

容器之间配置信息的传递,数据卷容器的生命周期一直持续到没有容器使用为止.

一旦你持久化到本地,这个时候本地的数据是不会删除的.



## Dockerfile

### 介绍

构建步骤:

- 编写一个dockerfile文件
- docker build构建成一个镜像
- docker run运行镜像
- docker push发布镜像

很多官方镜像都是基础包,我们通常会搭建自己的镜像

### 基础知识

- 关键词大写
- 执行从上到下
- #表示注释
- 每个指令都会创建提交一个新的镜像层,并提交

dockerfile是面向开发的,以后发布项目,做镜像,就需要编写dockerfile文件



Dockerfile:构建文件,定义了一切的步骤,源代码

DockerImages:通过Dockerfile构建生成的镜像,最终发布和运行的产品

Docker容器:容器就是镜像运行起来提供服务

### 指令

```shell
FROM #基础镜像,一切从这里开始构建

MAINTAINER #镜像作者

RUN #构建镜像时需要运行的命令

ADD #添加内容

WORKDIR #镜像工作目录

VOLUME #挂载目录

EXPOSE #暴露端口

CMD #指定容器启动时要运行的命令,只有只有最后一个会生效,可被替代

ENTRYPOINT #指定容器启动时要运行的命令,可以追加命令

ONBUILD #当构建一个被继承Dockerfile 这个时候就会运行ONBUILD命令,触发指令

COPY #类似ADD,将文件拷贝到镜像中

ENV #构建时设置环境变量
```

### 测试

DockerHub中99%的镜像都是从`scratch`来的,然后配置需要的软件和配置来构建

```shell
#1 编写dockerfile
FROM centos:7

MAINTAINER gyj

ENV MYPATH /usr/local
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

#2 构建镜像
docker build -f dockerfile路径 -t 镜像名:[tag] .

Successfully built d53f9a746e3d
Successfully tagged mycentos:0.1
```



列出镜像变更历史

```shell
root@DESKTOP-P6DO84T:~# docker history d53f9a746e3d
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
d53f9a746e3d   7 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B
89620245ae70   7 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
b12fa5e70139   7 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
44d35d1ff043   7 minutes ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
8460a0da60ad   7 minutes ago   /bin/sh -c yum -y install net-tools             156MB
6f279e76b99a   7 minutes ago   /bin/sh -c yum -y install vim                   210MB
48c1b98288b5   7 minutes ago   /bin/sh -c yum -y update                        290MB
c06e583215c6   7 minutes ago   /bin/sh -c #(nop) WORKDIR /usr/local            0B
c6441b2834c7   7 minutes ago   /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B
fe11550217d5   7 minutes ago   /bin/sh -c #(nop)  MAINTAINER gyj               0B
eeb6ee3f44bd   5 months ago    /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      5 months ago    /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>      5 months ago    /bin/sh -c #(nop) ADD file:b3ebbe8bd304723d4…   204MB
```



### CMD和ENTRYPOINT区别

cmd:

```shell
#编写dockerfile
FROM centos:7

CMD ["ls","-a"]

#运行镜像生成的容器
sudo docker run cmdtest
.
..
.dockerenv
anaconda-post.log
bin
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

#想追加一个命令 -l ,使命令结合起来变为=> ls -a -l
docker run cmdtest -l
#执行失败,因为-l把ls -a替换掉了
```

entrypoint:

```shell
#编写dockerfile
FROM centos:7

ENTRYPOINT ["ls","-a"]

#运行镜像生成的容器,并加-l
sudo docker run b6d762d55b19 -l
total 64
drwxr-xr-x   1 root root  4096 Feb 14 08:39 .
drwxr-xr-x   1 root root  4096 Feb 14 08:39 ..
-rwxr-xr-x   1 root root     0 Feb 14 08:39 .dockerenv
-rw-r--r--   1 root root 12114 Nov 13  2020 anaconda-post.log
lrwxrwxrwx   1 root root     7 Nov 13  2020 bin -> usr/bin
drwxr-xr-x   5 root root   340 Feb 14 08:39 dev
drwxr-xr-x   1 root root  4096 Feb 14 08:39 etc
drwxr-xr-x   2 root root  4096 Apr 11  2018 home
lrwxrwxrwx   1 root root     7 Nov 13  2020 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Nov 13  2020 lib64 -> usr/lib64
drwxr-xr-x   2 root root  4096 Apr 11  2018 media
drwxr-xr-x   2 root root  4096 Apr 11  2018 mnt
drwxr-xr-x   2 root root  4096 Apr 11  2018 opt
dr-xr-xr-x 274 root root     0 Feb 14 08:39 proc
dr-xr-x---   2 root root  4096 Nov 13  2020 root
drwxr-xr-x  11 root root  4096 Nov 13  2020 run
lrwxrwxrwx   1 root root     8 Nov 13  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root  4096 Apr 11  2018 srv
dr-xr-xr-x  11 root root     0 Feb 14 08:39 sys
drwxrwxrwt   7 root root  4096 Nov 13  2020 tmp
drwxr-xr-x  13 root root  4096 Nov 13  2020 usr
drwxr-xr-x  18 root root  4096 Nov 13  2020 var
```

### 实战:Tomcat镜像

1.准备镜像文件tomcat压缩包,jdk压缩包

![image-20220214165303921](picture\image-20220214165303921.png)

2.编写docker文件,官方命名`Dockerfile`

```shell
FROM centos:7

MAINTAINER gyj

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u311-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.58.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_311
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.58
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.58
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.58/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.58/bin/logs/catalina.out
```

3.构建镜像

```shell
docker build -t diytomcat .
```

4.运行容器

```shell
sudo docker run -d -p 9090:8080 --name tomcat -v /home/gyj/build/tomcat/test:/usr/local/apache-tomcat-9.0.58/webapps/test -v /home/gyj/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.58/logs 
```

5.访问9090端口



### 发布镜像

#### Dockerhub

1.注册和登录账号

```shell
sudo docker login -u goshino
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

2.服务器上提交镜像

```shell
#镜像名必须是账号名开头,否则上传失败
#可以用docker tag 修改镜像名
sudo docker push goshino/tomcat:1.0
The push refers to repository [docker.io/goshino/tomcat]
485d30b9f81f: Pushed
222bbe526ed0: Pushed
0fee5d75d500: Pushed
08d28eec6e0f: Pushed
174f56854903: Pushed
1.0: digest: sha256:edf00af0ca5e83b6d840d41a1ae34c1262515fd12ac7d983a398c6bc31a0fdf1 size: 1373
```

#### 阿里云镜像服务

1.登录阿里云

2.找到镜像服务

3.创建命名空间

4.创建镜像容器

参考官方文档



## Docker网络

### Docker0

使用ip addr查看网卡

![image-20220214190329129](picture\image-20220214190329129.png)

docker如何处理容器网络访问的?

```shell
docker run -d -P --name tomcat01 tomcat

#查看容器的ip地址
root@270fb158ee09:/usr/local/tomcat# ifconfig
#eth0是docker分配的ip地址
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 4453  bytes 9052929 (8.6 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2504  bytes 166764 (162.8 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        

#思考:linux能否ping通docker容器
ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.024 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.029 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.026 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.025 ms
64 bytes from 172.17.0.2: icmp_seq=5 ttl=64 time=0.025 ms
#结论:可以ping通
```

原理:

1.每启动一个docker容器,docker就会给docker容器分配一个ip,我们只要安装了docker,就会有一个网卡docker0,桥接模式,使用的技术是veth-pair技术!

再次测试ip addr

多出一个网卡

![image-20220214191846687](picture\image-20220214191846687.png)

2.再次启动一个容器,又多一个网卡

![image-20220215103254024](picture\image-20220215103254024.png)

```shell
#容器带来的网卡都是一对对的
#veth-pair就是一对的虚拟设备接口,他们都是成对出现的
#正因为有这个特性,veth-pair充当一个桥梁,连接各种虚拟网络设备的
#OpenStac,Docker容器之间的连接,OVS的连接,都是使用这个技术
```

3.测试tomcat01和tomcat02是否能ping通

```shell
#结论:容器和容器之间可以互相ping通
```

![image-20220215104644362](picture\image-20220215104644362.png)

结论:tomcat01和02是公用一个路由器,docker0

所有的容器在不指定网络的情况下,都是docker0路由的,docker会给容器分配一个默认可用ip



### 小结

Docker使用Linux桥接,宿主机中是一个Docker容器的网桥docker0

![image-20220215105313222](picture\image-20220215105313222.png)

Docker中所有的网络接口都是虚拟的,虚拟的转发效率高



### --link

思考一个场景:编写了一个微服务,database url=ip:,数据库ip变化之后,项目不需要重启,如果做到通过名字来访问容器

```shell
root@DESKTOP-P6DO84T:/home/gyj/tomcat# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

root@DESKTOP-P6DO84T:/home/gyj/tomcat# docker run -d -P --name tomcat03 --link tomcat02 tomcat
#现在就可用在03中ping通02了
root@90dced054f42:/usr/local/tomcat# ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.068 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.040 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=3 ttl=64 time=0.039 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=4 ttl=64 time=0.041 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=5 ttl=64 time=0.039 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=6 ttl=64 time=0.043 ms
^C
--- tomcat02 ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5164ms
rtt min/avg/max/mdev = 0.039/0.045/0.068/0.010 ms


#反方向可以ping通吗?
#不能
#其实这个tomcat03是在本地配置中增加了tomcat02的地址
root@DESKTOP-P6DO84T:/home/gyj/tomcat# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
172.17.0.3      tomcat02 f408f4442096
172.17.0.4      90dced054f42
```

**现在用Docker已经不建议使用--link了**

docker0问题:他不支持容器名连接访问!



### 自定义网路

查看所有的docker网络

```shell
root@DESKTOP-P6DO84T:/home/gyj/tomcat# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
2faf2789f59c   bridge    bridge    local
0f8a3eedf2d4   host      host      local
259b3005bedb   none      null      local
```

**网络模式**

bridge:桥接(默认,自己创建也使用bridge模式)

none:不配置网络

host:和宿主机共享网络

container:容器网络连通(用得少)

```shell
#docker创建容器默认使用桥接模式,以下两句相等
docker run -d -P --name tomcat01 tomcat
docker run -d -P --name tomcat01 --net bridge tomcat

#docker0特点:默认,域名不能访问,--link可以打通
#我们可以自己创建网络
#--driver bridge
#--subnet 192.168.0.0/16 最大可以255*255
#--gateway 192.168.0.1
root@DESKTOP-P6DO84T:~# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
2d17c20bbcb1ec10003d6c523665ff2ed8de59e2d0e863120241700ee9b4eac6
root@DESKTOP-P6DO84T:~# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
2faf2789f59c   bridge    bridge    local
0f8a3eedf2d4   host      host      local
2d17c20bbcb1   mynet     bridge    local
259b3005bedb   none      null      local
```

![image-20220215141124859](picture\image-20220215141124859.png)

创建两个使用自定义网络的容器

```shell
root@DESKTOP-P6DO84T:~# docker run -d -P --name tomcat-net-01 --net mynet tomcat
59baecc8d8c83a28aeb6ee965b9f1cf2c862f89cbf11b65c5f45d2de84b87155
root@DESKTOP-P6DO84T:~# docker run -d -P --name tomcat-net-02 --net mynet tomcat
cab01bd419e36c12859f7d71a55867c5c9bfbbca213275dc19cff5c33990857f
```

![image-20220215141432578](picture\image-20220215141432578.png)

现在容器之间是可以互相ping通的

```shell
docker exec -it tomcat-net-02 ping tomcat-net-01
```

我们自定义的网络docker都已经帮我们维护好了对应关系,推荐平时这样使用网络



好处:

redis/mysql:不同集群使用不同网络,保证集群是安全和健康的



### 网络连通

将不同网段的容器或者网络连接起来

![image-20220215142909790](picture\image-20220215142909790.png)



```shell
#连通tomcat01到mynet中
root@DESKTOP-P6DO84T:~# docker network connect mynet tomcat01

root@DESKTOP-P6DO84T:~# docker network inspect mynet
#一个容器两个ip地址
```

![image-20220215143408720](picture\image-20220215143408720.png)



### 实战:部署redis集群

![image-20220215144350354](picture\image-20220215144350354.png)

```shell
#脚本创建6个redis配置
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF > /mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done

# 通过脚本一次启动6个redis容器
for port in $(seq 1 6); \
do \
docker run -p 637${port}:6379 -p 1637${port}:16379 --name redis-${port} \
-v /mydata/redis/node-${port}/data:/data \
-v /mydata/redis/node-${port}/conf/redis.conf:/etc/redis/redis.conf \
-d --net redis --ip 172.38.0.1${port} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
done
```

![image-20220215150737800](picture\image-20220215150737800.png)

```shell
docker exec -it redis-1 sh
/data # ls
appendonly.aof  nodes.conf
/data # redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 1
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
M: daa108185bb267abbafe45291da4b2a1c5a44e40 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
M: c0430c6a7ff9883eea1412c762f546402520a0f7 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
M: 987bd00566ac54810111e68d49fde3e2c56881a3 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
S: bc61a6f8b41a1d55be45268780a4965728671a2a 172.38.0.14:6379
   replicates 987bd00566ac54810111e68d49fde3e2c56881a3
S: 3b6244c083556c44bb3fae5ba5d1d44ca2cc377e 172.38.0.15:6379
   replicates daa108185bb267abbafe45291da4b2a1c5a44e40
S: 9d83cd7755598452699bae52719ad53acecbcea5 172.38.0.16:6379
   replicates c0430c6a7ff9883eea1412c762f546402520a0f7
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
..
>>> Performing Cluster Check (using node 172.38.0.11:6379)
M: daa108185bb267abbafe45291da4b2a1c5a44e40 172.38.0.11:6379
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 3b6244c083556c44bb3fae5ba5d1d44ca2cc377e 172.38.0.15:6379
   slots: (0 slots) slave
   replicates daa108185bb267abbafe45291da4b2a1c5a44e40
S: bc61a6f8b41a1d55be45268780a4965728671a2a 172.38.0.14:6379
   slots: (0 slots) slave
   replicates 987bd00566ac54810111e68d49fde3e2c56881a3
M: c0430c6a7ff9883eea1412c762f546402520a0f7 172.38.0.12:6379
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 9d83cd7755598452699bae52719ad53acecbcea5 172.38.0.16:6379
   slots: (0 slots) slave
   replicates c0430c6a7ff9883eea1412c762f546402520a0f7
M: 987bd00566ac54810111e68d49fde3e2c56881a3 172.38.0.13:6379
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

查看集群信息

```shell
/data # redis-cli -c
127.0.0.1:6379> cluster info
cluster_state:ok
cluster_slots_assigned:16384
cluster_slots_ok:16384
cluster_slots_pfail:0
cluster_slots_fail:0
cluster_known_nodes:6
cluster_size:3
cluster_current_epoch:6
cluster_my_epoch:1
cluster_stats_messages_ping_sent:227
cluster_stats_messages_pong_sent:233
cluster_stats_messages_sent:460
cluster_stats_messages_ping_received:228
cluster_stats_messages_pong_received:227
cluster_stats_messages_meet_received:5
cluster_stats_messages_received:460
127.0.0.1:6379> cluster nodes
3b6244c083556c44bb3fae5ba5d1d44ca2cc377e 172.38.0.15:6379@16379 slave daa108185bb267abbafe45291da4b2a1c5a44e40 0 1644908963000 5 connected
bc61a6f8b41a1d55be45268780a4965728671a2a 172.38.0.14:6379@16379 slave 987bd00566ac54810111e68d49fde3e2c56881a3 0 1644908962376 4 connected
c0430c6a7ff9883eea1412c762f546402520a0f7 172.38.0.12:6379@16379 master - 0 1644908963000 2 connected 5461-10922
daa108185bb267abbafe45291da4b2a1c5a44e40 172.38.0.11:6379@16379 myself,master - 0 1644908961000 1 connected 0-5460
9d83cd7755598452699bae52719ad53acecbcea5 172.38.0.16:6379@16379 slave c0430c6a7ff9883eea1412c762f546402520a0f7 0 1644908963378 6 connected
987bd00566ac54810111e68d49fde3e2c56881a3 172.38.0.13:6379@16379 master - 0 1644908962577 3 connected 10923-16383
```

设置值
```shell
127.0.0.1:6379> set a b
-> Redirected to slot [15495] located at 172.38.0.13:6379 #redis-3节点处理了这个操作
OK
```

停止redis-3

```shell
root@DESKTOP-P6DO84T:~# docker stop redis-3
redis-3
```

获取值

```shell
#重新连接到集群
#redis-4成为master
/data # redis-cli -c
127.0.0.1:6379> cluster nodes
3b6244c083556c44bb3fae5ba5d1d44ca2cc377e 172.38.0.15:6379@16379 slave daa108185bb267abbafe45291da4b2a1c5a44e40 0 1644909283080 5 connected
bc61a6f8b41a1d55be45268780a4965728671a2a 172.38.0.14:6379@16379 master - 0 1644909284083 7 connected 10923-16383
c0430c6a7ff9883eea1412c762f546402520a0f7 172.38.0.12:6379@16379 master - 0 1644909284585 2 connected 5461-10922
daa108185bb267abbafe45291da4b2a1c5a44e40 172.38.0.11:6379@16379 myself,master - 0 1644909283000 1 connected 0-5460
9d83cd7755598452699bae52719ad53acecbcea5 172.38.0.16:6379@16379 slave c0430c6a7ff9883eea1412c762f546402520a0f7 0 1644909284000 6 connected
987bd00566ac54810111e68d49fde3e2c56881a3 172.38.0.13:6379@16379 master,fail - 1644909208209 1644909206505 3 connected
#获取到了a的值
127.0.0.1:6379> get a
-> Redirected to slot [15495] located at 172.38.0.14:6379
"b"
```

部署成功!



## Docker Compose

### 简介

定义和运行多个容器

使用YAML文件

三步骤:

1. 定义Dockerfile,让它能在任何环境运行
2. 定义docker-compose.yml去执行你的程序,让他们能在一个隔离的环境中运行
3. 启动项目 `docker-compose up`

作用:批量容器编排

重要的概念:

1. 服务services 容器,应用
2. 项目project 一组关联的容器

模板

```yaml
version: "3.9"  # optional since v1.27.0
services:
  web:
    build: .
    ports:
      - "8000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    links:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}
```

### linux安装

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose
```



### 体验

```shell
 mkdir composetest
 cd composetest
 
#创建app.py文件
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count)
    
#创建requirements.txt
flask
redis

#创建Dockerfile
# syntax=docker/dockerfile:1
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
EXPOSE 5000
COPY . .
CMD ["flask", "run"]

#创建docker-compose.yml
version: "3.9"
services:
  web:
    build: .
    ports:
      - "8000:5000"
  redis:
    image: "redis:alpine"
    
#运行
#docker-compose build(可选)
docker-compose up

http://localhost:8000/
```

![image-20220215163051480](picture\image-20220215163051480.png)

![image-20220215163206908](picture\image-20220215163206908.png)

默认服务名

文件名\_服务名_num



docker-copose会自动创建一个网络

![image-20220215163337953](picture\image-20220215163337953.png)

![image-20220215163506059](picture\image-20220215163506059.png)



**Docker小结:**

1. Docker镜像  run => 容器
2. Dockerfile构建镜像(服务打包)
3. docker-compose启动项目(编排,多个微服务环境)
4. Docker网络



### 规则

```yaml
#3层

version: '' #版本,和docker版本号相关
services: #服务
	服务1:
		build: #配置
		depends_on: #规定先启动的服务
	服务2:
	服务3:
	...
```

官网查找命令详细用法:

https://docs.docker.com/compose/



### 开源项目

```shell
cd my_wordpress/

#docker-compose.yml
version: "3.9"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
  
  
  
docker-compose up -d #后台启动
```



## Docker Swarm

### 购买服务器

### 搭建

生成主节点init

加入(管理者,worker)

### Raft协议

保证大多数节点存活才可以用,集群至少大于3台

少于3台就无法运行

worker节点无法使用管理命令

大于1台管理节点存活

### 操作

```shell
#集群中创建一个nginx服务
docker service create -p 8888:80 --name my-nginx nginx

#动态扩缩容
#生成3个nginx,随时调整数量
docker service update --replicas 3 my-nginx

#移除服务
docker service rm my-nginx
```



命令 -> 管理 -> api -> 调度 -> 工作节点(创建Task容器维护创建)



## Docker Stack

集群部署

```shell
#单机
docker-compose up -d wordpress.yml
#集群
docker stack deploy wordpress.yml
```



## Docker Secret



## Docker Config



## 扩展到K8S

### 云原生

