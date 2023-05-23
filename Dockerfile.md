# Dockerfile介绍
dockerfile是用来构建docker镜像的文件！命令参数脚本！
构建步骤：

1. 编写一个dockerfile文件
2. docker build 构建成为一个镜像
3. docker run 运行镜像
4. docker push 发布镜像（DokcerHub、阿里云镜仓库）

很多官方的镜像都是基础包，很多功能没有，我们通常会自己搭建自己的镜像！

# Dockerfile构建过程
基础知识：

1. 每个保留关键字（指令）都是必须大写字母
2. 执行从上到下顺序执行
3. #表示注释
4. 每一个指令都表示一层镜像！

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682662964674-130570de-d9d6-4c71-8c3d-cf0eda267513.png#averageHue=%23ece9e5&clientId=u571db970-57df-4&from=paste&height=407&id=u895eb28a&originHeight=509&originWidth=1015&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=174729&status=done&style=none&taskId=ubd086b03-d399-414f-a2ef-3d06e1354ca&title=&width=812)
dockerfile是面向开发的，我们以后要开发项目，就需要发布镜像，写Dockerfile
Docker镜像逐渐成为企业交付的标准，必须掌握！

Dockerfile：构建文件，定义了一切的步骤，源代码
DockerImages：通过Dockerfile构建生成的镜像，最终发布和运行的产品
Docker容器：容器就是镜像运行起来提供的服务器
# Dockerfile指令
```shell
FROM         # 基础镜像，一切从这里开始构建
MAINTAINER   # 镜像是谁写的，姓名 + 邮箱
RUN          # 镜像构建的时候需要运行的指令
ADD          # 步骤，tomcat镜像，这个tomcat压缩包，添加内容
WORKDIR      # 镜像的工作目录
VOLUME       # 挂载的目录
EXPOSE       # 保留端口设置
CMD          # 指定这个容器启动的时候要运行的命令，只有最后一个会生效
EXTRYPOINT   # 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD      # 当构建一个被继承的Dockerfile，这个时候就会运行 ONBUILD 的指令。触发指令
COPY         # 和ADD类似，将我们的文件拷贝到镜像中
ENV          # 构建的时候设置环境变量
```
# 实战测试
> 创建自己的centos

```shell
[root@VM-4-5-centos dockerfile]# cat mydockerfile-centos 
FROM centos
MAINTAINER poison02<2069820192@qq.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH

# RUN yum -y install vim  # 这里注释是因为Centos不在维护镜像了，所以这里需要后续改一下mirrorlist，我直接就不要这两行了。。。
# RUN yum -y install net-tools

EXPOSE 80

CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash

```
```shell
[root@VM-4-5-centos dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .[+] Building 0.1s (6/6) FINISHED                                                      
 => [internal] load build definition from mydockerfile-centos                    0.0s
 => => transferring dockerfile: 204B                                             0.0s
 => [internal] load .dockerignore                                                0.0s
 => => transferring context: 2B                                                  0.0s
 => [internal] load metadata for docker.io/library/centos:latest                 0.0s
 => [1/2] FROM docker.io/library/centos                                          0.0s
 => CACHED [2/2] WORKDIR /usr/local                                              0.0s
 => exporting to image                                                           0.0s
 => => exporting layers                                                          0.0s
 => => writing image sha256:0fada5966706496958cd2a729cf9e67ca19975c4ae19f6ce719  0.0s
 => => naming to docker.io/library/mycentos:0.1  
```
```shell
[root@VM-4-5-centos dockerfile]# docker run -it mycentos:0.1
[root@447b9183cbd7 local]# pwd # 这里发现已经到了预先设置好的工作目录 /usr/local下了
/usr/local
```
## ERROR
解决上面dockerfile中`RUN yum -y install vim`失败的问题：
```shell
# 首先进入目录
cd /etc/yum.repos.d/

# 更换mirror
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*

# 生成缓存更新
yum makecache

# 更新yum以及安装vim
yum update -y
yum -y install vim

```
我们可以列出本地进行的变更历史
## CMD 和 ENTRYPOINT 的区别
> CMD 测试

```shell
# mydockerfile-test-centos 文件
FROM centos
CMD ["ls", "-a"]


[root@VM-4-5-centos dockerfile]# docker build -f mydockerfile-test-centos -t cmdtest .[+] Building 0.1s (5/5) FINISHED                                                      
 => [internal] load .dockerignore                                                0.0s
 => => transferring context: 2B                                                  0.0s
 => [internal] load build definition from mydockerfile-test-centos               0.0s
 => => transferring dockerfile: 82B                                              0.0s
 => [internal] load metadata for docker.io/library/centos:latest                 0.0s
 => CACHED [1/1] FROM docker.io/library/centos                                   0.0s
 => exporting to image                                                           0.0s
 => => exporting layers                                                          0.0s
 => => writing image sha256:ec476b75609ab1b6a24084872ad4908bff7259890997a811203  0.0s
 => => naming to docker.io/library/cmdtest                                       0.0s
[root@VM-4-5-centos dockerfile]# docker run cmdtest # 运行容器可以看到运行了我们在dockerfile中写的命令
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
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


# 向追加一个命令 -l   ls -al
[root@VM-4-5-centos dockerfile]# docker run ec476b75609a -l
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-l": executable file not found in $PATH: unknown.
ERRO[0000] error waiting for container:

# cmd的情况下， -l 替换了CMD的命令，而-l不是命令，所以报错了

```
> ENTRYPOINT

```shell
# dockerfile-entrypoint-centos的文件内容
FROM centos
ENTRYPOINT ["ls", "-a"]


[root@VM-4-5-centos dockerfile]# docker build -f dockerfile-entrypoint-centos -t entrytest .
[+] Building 0.1s (5/5) FINISHED                                                      
 => [internal] load .dockerignore                                                0.0s
 => => transferring context: 2B                                                  0.0s
 => [internal] load build definition from dockerfile-entrypoint-centos           0.0s
 => => transferring dockerfile: 91B                                              0.0s
 => [internal] load metadata for docker.io/library/centos:latest                 0.0s
 => CACHED [1/1] FROM docker.io/library/centos                                   0.0s
 => exporting to image                                                           0.0s
 => => exporting layers                                                          0.0s
 => => writing image sha256:d21eefc30213be22f2a4dbb9065eff67e47465cedf32c71f492  0.0s
 => => naming to docker.io/library/entrytest                                     0.0s
[root@VM-4-5-centos dockerfile]# docker run entrytest  # 这里和CMD还没有区别
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
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
[root@VM-4-5-centos dockerfile]# docker run entrytest -l  # 这里区别就出现了，这里是追加命令！
total 56
drwxr-xr-x   1 root root 4096 Apr 28 07:19 .
drwxr-xr-x   1 root root 4096 Apr 28 07:19 ..
-rwxr-xr-x   1 root root    0 Apr 28 07:19 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  3  2020 bin -> usr/bin
drwxr-xr-x   5 root root  340 Apr 28 07:19 dev
drwxr-xr-x   1 root root 4096 Apr 28 07:19 etc
drwxr-xr-x   2 root root 4096 Nov  3  2020 home
lrwxrwxrwx   1 root root    7 Nov  3  2020 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  3  2020 lib64 -> usr/lib64
drwx------   2 root root 4096 Sep 15  2021 lost+found
drwxr-xr-x   2 root root 4096 Nov  3  2020 media
drwxr-xr-x   2 root root 4096 Nov  3  2020 mnt
drwxr-xr-x   2 root root 4096 Nov  3  2020 opt
dr-xr-xr-x 106 root root    0 Apr 28 07:19 proc
dr-xr-x---   2 root root 4096 Sep 15  2021 root
drwxr-xr-x  11 root root 4096 Sep 15  2021 run
lrwxrwxrwx   1 root root    8 Nov  3  2020 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov  3  2020 srv
dr-xr-xr-x  13 root root    0 Apr 11 00:50 sys
drwxrwxrwt   7 root root 4096 Sep 15  2021 tmp
drwxr-xr-x  12 root root 4096 Sep 15  2021 usr
drwxr-xr-x  20 root root 4096 Sep 15  2021 var
```
## 实战：Tomcat镜像
1、准备镜像文件 tomcat 压缩包 jdk压缩包
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1684823667383-06a58e71-f8d6-454c-8c10-bc3514614e74.png#averageHue=%23100907&clientId=u8428adc1-f62d-4&from=paste&height=83&id=u8c2e25df&originHeight=83&originWidth=546&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8874&status=done&style=none&taskId=u8bdfd45d-96d0-48d0-acdc-bb6042a7e63&title=&width=546)
2、编写Dockerfile文件，官方命名为Dockerfile，build自动寻找
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1684823700310-a1241c7c-6557-4910-92fc-2be0aabdbda0.png#averageHue=%230a0604&clientId=u8428adc1-f62d-4&from=paste&height=46&id=ucc1ab299&originHeight=46&originWidth=531&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3096&status=done&style=none&taskId=u8fe815b2-b32a-4c7d-b844-75d917d9562&title=&width=531)
```shell
FROM centos
MAINTAINET poison<2069820192@qq.com>
COPY README.md /usr/local/README.md
ADD jdk-8u361-linux-x64.tar.gz /usr/local
ADD apache-tomcat-9.0.45.tar.gz /usr/local

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_361
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.45
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.45
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.45/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.45/bin/logs/catalina.o
ut

```
 3、构建镜像，通过 `docker build -t diytomcat .`构建
4、启动镜像
`docker run -d -p 9090:8080 --name poisontomcat -v /home/tomcat/test:/usr/local/apache-tomcat-9.0.45/webapps/test -v /home/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.45/logs diytomcat`
5、访问测试
访问ip + 9090即可看到tomcat
6、发布项目，由于做了卷挂载，直接在本地就可以发布了
首先在本地的`test`目录新建这两个文件，下面代码贴上去进行测试
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1684825909414-d5d45191-541d-4074-a3b4-00e7d050d60f.png#averageHue=%230d0a07&clientId=u8428adc1-f62d-4&from=paste&height=81&id=u51387e13&originHeight=81&originWidth=368&originalType=binary&ratio=1&rotation=0&showTitle=false&size=5615&status=done&style=none&taskId=u61cc2f65-e75f-409d-bcb3-4693fe30640&title=&width=368)
```xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

  

</web-app>
```
```xml
<%@ page contentType="text/html;charset=UTF-8" language="java" isELIgnored="false" %>
<html>
<body>
<h2>Hello World!</h2>
</body>
</html>
```
写完之后保存，然后浏览器访问 `ip:9090/test`即可看到`Hello World！`
## 发布自己的镜像
> DockerHub

注册账号，在服务器上提交自己的镜像
通过命令行登录
```bash
[root@VM-4-5-centos test]# docker login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
```
登录之后即可 `docker push`
```shell
[root@VM-4-5-centos test]# docker login -u poison02
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```
```shell
docker tag 73e9c7434574 poison02/tomcat:1.0
docker push poison02/tomcat:1.0
# 执行以上两条命令即可发布，只需等待
```
## 发布镜像到阿里云
进入阿里云的 `容器镜像服务`，创建命名空间和镜像仓库
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1684827398014-80f17b61-18bc-484e-bc3c-518da444f7c6.png#averageHue=%23fcfcfb&clientId=u8428adc1-f62d-4&from=paste&height=297&id=udda767c2&originHeight=297&originWidth=756&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18695&status=done&style=none&taskId=ub07a5cac-bf58-43e1-84e0-17b267a296a&title=&width=756)
然后根据操作指南操作即可，
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1684827407943-022a81ee-ee65-443c-9ad8-17acd7de738b.png#averageHue=%23e5c99f&clientId=u8428adc1-f62d-4&from=paste&height=558&id=u6178bf16&originHeight=558&originWidth=980&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52978&status=done&style=none&taskId=uad016e12-c077-4a92-9d7e-a0edabd7c06&title=&width=980)
# Docker全流程
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1684827879703-7a9b6cf6-d50e-4b76-954f-74e6f9f13a59.png#averageHue=%23d9d9d7&clientId=u8428adc1-f62d-4&from=paste&height=676&id=u2c9b829e&originHeight=676&originWidth=802&originalType=binary&ratio=1&rotation=0&showTitle=false&size=281792&status=done&style=none&taskId=ufeead68d-ee09-4e69-a72c-bb830b90804&title=&width=802)
