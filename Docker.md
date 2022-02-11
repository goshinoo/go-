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

![image-20220211182602849](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20220211182602849.png)



### commit镜像

```shell
docker commit 提交容器成为一个新的副本

-m 描述信息
-a 作者

C:\Users\admin>docker commit -m loginserver -a gyj d22ffc21b69a loginserver:v1
sha256:8a7dbbc785e2c49d4ac9c65b676c5c6496c59febcef734f9367e5dbc6604c3de
```

