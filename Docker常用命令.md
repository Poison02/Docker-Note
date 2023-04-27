# 帮助命令
```shell
docker version # 显示Docker版本信息
docker info    # 显示Docker系统信息，包括镜像和容器的数量
docker 命令 --help # 帮助命令
```
帮助文档的地址：[https://docs.docker.com/reference/](https://docs.docker.com/reference/)
# 镜像命令
```shell
[root@VM-4-5-centos ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   19 months ago   13.3kB

# 解释：
Repository  镜像的仓库源
TAG          镜像的标签
IMAGE ID 镜像的id
CREATED 镜像创建时间
SIZE 镜像的大小

# 可选项 
-a, --all            显示所有镜像
-q, --quiet          只显示镜像ID


```
```shell
[root@VM-4-5-centos ~]# docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   14072     [OK]       
mariadb                         MariaDB Server is a high performing open sou…   5370      [OK]       


# 可选项, 通过收藏过滤
docker search mysql --filter=STARS=3000   # 搜索出来的镜像就是STARS大于3000的


```
```shell
[root@VM-4-5-centos ~]# docker pull mysql
Using default tag: latest # 如果不写tag，默认下载最新的
latest: Pulling from library/mysql 
328ba678bf27: Pull complete  # 分层下载 docker images的核心 联合文件系统
f3f5ff008d73: Pull complete 
dd7054d6d0c7: Pull complete 
70b5d4e8750e: Pull complete 
cdc4a7b43bdd: Pull complete 
a0608f8959e0: Pull complete 
5823e721608f: Pull complete 
a564ada930a9: Pull complete 
539565d00e89: Pull complete 
a11a06843fd5: Pull complete 
92f6d4aa041d: Pull complete 
Digest: sha256:a43f6e7e7f3a5e5b90f857fbed4e3103ece771b19f0f75880f767cf66bbb6577
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest # 真实地址

# 等价于它
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载（版本信息必须在 ”DockerHub“上面存在）
docker pull mysql:5.7
```
```shell
docker rmi -f 镜像id # 删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id... # 删除多个镜像
docker rmi -f $(docker images -aq) # 删除全部镜像
```
# 容器命令
我们需要有镜像才可以创建容器，现在下载一个Linux镜像来测试学习。
```shell
docker pull centos
```
```shell
docker run [可选参数] image

# 参数说明
--name="Name"  容器名字 tomcat01 tomcat02 用于区分容器
-d             后台方式运行
-it            使用交互方式运行，进入容器查看内容
-p             指定容器的端口 -p  8080:8080
    -p ip：主机端口：容器端口
    -p 主机端口：容器端口（常用）
    -p 容器端口
    容器端口
-P              随机指定端口

# 测试，启动并进入容器
[root@VM-4-5-centos ~]# docker run -it centos /bin/bash
[root@7d447e5a05f2 /]# ls # 查看容器内的centos 基础版本，很多命令都不完善
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@7d447e5a05f2 /]# exit # 从容器中退出
exit
```
```shell
# docker ps 命令  # 列出当前正在运行的容器
-a  # 列出当前正在运行的容器和历史运行的容器
-n=? # 显示最近创建的容器
-q  # 只显示容器的编号

[root@VM-4-5-centos ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@VM-4-5-centos ~]# docker ps -a
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                          PORTS     NAMES
7d447e5a05f2   centos         "/bin/bash"   2 minutes ago   Exited (0) About a minute ago             goofy_lewin
672f38a28bc7   feb5d9fea6a5   "/hello"      24 hours ago    Exited (0) 24 hours ago                   wonderful_brahmagupta                 wonderful_brahmagupta
```
```shell
exit # 直接停止容器并退出
Ctrl + P + Q  # 容器不停止退出
```
```shell
docker rm 容器id   # 删除指定容器，不能删除正在运行的容器
docker rm -f $(docker ps -aq)   # 删除所有的容器
docker ps -a -q|xargs docker rm  # 删除所有的容器
```
```shell
docker start 容器id  # 启动容器
docker restart 容器id  # 重启容器
docker stop 容器id  # 停止容器
docker kill 容器id  # 强制停止容器
```
# 常用其他命令
```shell
docker run -d 镜像名

# 问题：docker ps 发现centos停止了
# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker没有发现应用，就会自动停止
# 如：nginx，容器启动后，发现自己没有提供服务，就会立即停止，就是没有程序了
```
```shell
docker logs
-tf   # 显示日志
--tail # 显示详细信息
```
```shell
docker top 容器id

[root@VM-4-5-centos ~]# docker top 9c643a669576
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                5015                4995                0                   14:52               pts/0               00:00:00            /bin/bash
```
```shell
docker inspect

# 测试
[root@VM-4-5-centos ~]# docker inspect 9c643a669576
[
    {
        "Id": "9c643a669576556e20e3adb799500ad6d6be552d29bc0724ba04bc2c0ba5e142",
        "Created": "2023-04-26T06:41:26.408400797Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 5015,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-04-26T06:52:58.062462723Z",
            "FinishedAt": "2023-04-26T06:41:48.932704343Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/9c643a669576556e20e3adb799500ad6d6be552d29bc0724ba04bc2c0ba5e142/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/9c643a669576556e20e3adb799500ad6d6be552d29bc0724ba04bc2c0ba5e142/hostname",
        "HostsPath": "/var/lib/docker/containers/9c643a669576556e20e3adb799500ad6d6be552d29bc0724ba04bc2c0ba5e142/hosts",
        "LogPath": "/var/lib/docker/containers/9c643a669576556e20e3adb799500ad6d6be552d29bc0724ba04bc2c0ba5e142/9c643a669576556e20e3adb799500ad6d6be552d29bc0724ba04bc2c0ba5e142-json.log",
        "Name": "/wizardly_almeida",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                41,
                109
            ],
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
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
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
                "LowerDir": "/var/lib/docker/overlay2/ff786fb866c6db99c4b850b92969ef8b37d6a0e2ba451abb2b407799783164e4-init/diff:/var/lib/docker/overlay2/c528f83b98f8f381239d9309e5fc5e33abfb53e825ebea9c787f0c160cf282aa/diff",
                "MergedDir": "/var/lib/docker/overlay2/ff786fb866c6db99c4b850b92969ef8b37d6a0e2ba451abb2b407799783164e4/merged",
                "UpperDir": "/var/lib/docker/overlay2/ff786fb866c6db99c4b850b92969ef8b37d6a0e2ba451abb2b407799783164e4/diff",
                "WorkDir": "/var/lib/docker/overlay2/ff786fb866c6db99c4b850b92969ef8b37d6a0e2ba451abb2b407799783164e4/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "9c643a669576",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "b23659e3aeb1f5ecd7d4e2057e46823fc8443c6712ec955f1add35af7293ca2d",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/b23659e3aeb1",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "7819aec1aa7277090d7b48ffea60d3f2c9fce699a86cf3228413693295d35313",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "5156c549251cf6b74d2ce738c6ae4429cefa1597398967e32da036fde7b30d92",
                    "EndpointID": "7819aec1aa7277090d7b48ffea60d3f2c9fce699a86cf3228413693295d35313",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```
```shell
# 通常容器都是使用后台运行的，需要进入容器，修改一些配置

# 命令
docker exec -it 容器id bashshell

# 测试
[root@VM-4-5-centos ~]# docker exec -it 9c643a669576 /bin/bash
[root@9c643a669576 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@9c643a669576 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 06:52 pts/0    00:00:00 /bin/bash
root        15     0  0 06:57 pts/1    00:00:00 /bin/bash
root        30    15  0 06:57 pts/1    00:00:00 ps -ef

# 方式二
docker attach 容器id

# 两种方式区别
# docker exec  # 进入容器后开启一个新的中断，可以操作
# docker attach # 进入容器正在执行的终端，不会启动新的进程
```
```shell
docker cp 容器id:容器内路径 主机路径

# 测试
[root@VM-4-5-centos /]# docker ps 
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
ccb8272501df   centos    "/bin/bash"   2 minutes ago   Up 2 minutes             peaceful_lamport
[root@VM-4-5-centos /]# docker attach ccb8272501df # 进入容器
[root@ccb8272501df /]# cd /home
[root@ccb8272501df home]# ls
[root@ccb8272501df home]# touch test.java # 新建文件
[root@ccb8272501df home]# exit
exit
[root@VM-4-5-centos /]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@VM-4-5-centos /]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
ccb8272501df   centos    "/bin/bash"   3 minutes ago   Exited (0) 6 seconds ago             peaceful_lamport
[root@VM-4-5-centos /]# docker cp ccb8272501df:/home/test.java /home # 拷贝文件到主机
Preparing to copy...
Successfully copied 1.536kB to /home
[root@VM-4-5-centos /]# cd /home
[root@VM-4-5-centos home]# ls # 查看主机的文件
dockerzch  test.java

# 拷贝是一个手动过程，未来我们使用 -V 卷的技术，可以实现
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682493006937-a92e2c52-a67c-478b-8dfd-aa63e0e3621a.png#averageHue=%23276ea3&clientId=u373f7e36-c55b-4&from=paste&height=719&id=u8795fc08&originHeight=719&originWidth=1037&originalType=binary&ratio=1&rotation=0&showTitle=false&size=286368&status=done&style=none&taskId=u573c88a7-8700-4250-9013-2e0b3a1851b&title=&width=1037)
# 作业练习
## Docker 安装Nginx
```shell
docker search nginx # 建议去DockerHub搜索

```
```shell
docker pull nginx
```
```shell
[root@VM-4-5-centos home]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
nginx        latest    6efc10a0510f   13 days ago     142MB
centos       latest    5d0da3dc9764   19 months ago   231MB
# -d 后台运行
# --name 容器命名
# -p 宿主机端口：容器内端口
[root@VM-4-5-centos home]# docker run -d --name nginx01 -p:80:80 nginx # 运行nginx
2be000bed78934a17725fc2c0de6bced67e21266b2634acb8ad826183093afd4
[root@VM-4-5-centos home]# curl localhost:80 # 测试nginx
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

# 进入容器
[root@VM-4-5-centos home]# docker exec -it nginx01 /bin/bash
root@2be000bed789:/# ls
bin   dev		   docker-entrypoint.sh  home  lib64  mnt  proc  run   srv  tmp  var
boot  docker-entrypoint.d  etc			 lib   media  opt  root  sbin  sys  usr
root@2be000bed789:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@2be000bed789:/# cd /etc/nginx
root@2be000bed789:/etc/nginx# ls
conf.d	fastcgi_params	mime.types  modules  nginx.conf  scgi_params  uwsgi_params
```
## Docker安装Tomcat
```shell
docker run -it --rm tomcat:9.0
# 这个意思是后台启动，且用完即删除
```
```shell
# 下载镜像
docker pull tomcat
# 运行容器
docker -d -p 8888:8080 --name tomcat01 tomcat
```
访问：ip:8888 即可访问到tomcat的页面。
## Docker安装ES和Kibana
```shell
# es特别耗内存
# es暴露的端口很多
# 数据需要放在安全目录内
# --net somenetwork 网络配置
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:8.6.2
```
```shell
docker stats # 查看CPU运行状态

# 增加内存限制
docker run -d --name elasticsearch02 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xms512m" elasticsearch:8.6.2
```
# 可视化
portainer，Docker的图形化界面管理工具，提供一个后台面板供我们操作
```shell
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock --privileged=true portainer/portainer
```
访问测试：
`ip:8088`访问即可
设置账号密码即可进入：
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682572775271-0656200c-2706-4e07-9f24-a8929477fcdf.png#averageHue=%23e3ddc1&clientId=u5e02f36e-b090-4&from=paste&height=901&id=u35101e5f&originHeight=901&originWidth=1907&originalType=binary&ratio=1&rotation=0&showTitle=false&size=177123&status=done&style=none&taskId=u98ce40f2-e491-4410-ab7f-3c656459b92&title=&width=1907)

