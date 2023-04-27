# 镜像是什么？
镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件。
所有的应用，直接打包docker镜像，就可以直接跑起来！
如何得到镜像：

- 从远程操哭下载
- 朋友拷贝
- 自己制作镜像 DockerFile
# Docker镜像加载原理
> UnionFS（联合文件系统）

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682572951653-3c46e48f-dc68-4868-aafb-428bb4b3b2c0.png#averageHue=%23dad7d0&clientId=ue5b496cb-8ad5-4&from=paste&height=201&id=u017860ce&originHeight=201&originWidth=1138&originalType=binary&ratio=1&rotation=0&showTitle=false&size=183368&status=done&style=none&taskId=uc882d88d-10e1-429f-b779-a78b263c2e5&title=&width=1138)
我们在下载的时候看到的一层层安装就是这个

> Docker镜像加载原理

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682573251105-da2498cb-be79-4f80-9488-460aa36d09fd.png#averageHue=%23d4dfda&clientId=ue5b496cb-8ad5-4&from=paste&height=565&id=u33405416&originHeight=565&originWidth=1145&originalType=binary&ratio=1&rotation=0&showTitle=false&size=345501&status=done&style=none&taskId=u73c8e017-63bc-4676-876c-075d3e9f85c&title=&width=1145)
# 分层理解
我们在下载某个镜像时，可以看到都是分层按需下载的，意思就是已有的东西就不再安装，只需要安装没有的。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682573350049-e0bfe949-9720-420a-b491-b4793965d283.png#averageHue=%230d0a07&clientId=ue5b496cb-8ad5-4&from=paste&height=216&id=uafc1f8ec&originHeight=216&originWidth=570&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18126&status=done&style=none&taskId=u42df239f-243f-4a95-80c3-573416b764b&title=&width=570)
为什么Docker安装要分层安装？
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682573466247-fbd8f0e9-e844-4f85-a561-1334c4508c5f.png#averageHue=%23f9f9f9&clientId=ue5b496cb-8ad5-4&from=paste&height=643&id=u472702e2&originHeight=643&originWidth=1181&originalType=binary&ratio=1&rotation=0&showTitle=false&size=230644&status=done&style=none&taskId=u6e89fff1-141c-46bc-892d-f90aae45afb&title=&width=1181)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682573585950-8053d805-8e29-4c9e-96d5-dbe46600e362.png#averageHue=%23e1e0dd&clientId=ue5b496cb-8ad5-4&from=paste&height=436&id=u2d6b456d&originHeight=436&originWidth=1171&originalType=binary&ratio=1&rotation=0&showTitle=false&size=123605&status=done&style=none&taskId=u8139182a-02b0-42a7-8576-c611f0c085c&title=&width=1171)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682573599041-1d26a1dd-6d44-4fab-8b8c-283b9a560943.png#averageHue=%23eae9e7&clientId=ue5b496cb-8ad5-4&from=paste&height=599&id=ueaa183c0&originHeight=599&originWidth=1125&originalType=binary&ratio=1&rotation=0&showTitle=false&size=161346&status=done&style=none&taskId=uf9ed093f-ec48-4f19-9e21-c34b1dba312&title=&width=1125)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682573605792-5b664494-632a-423a-844f-4d99344ae353.png#averageHue=%23f5f5f5&clientId=ue5b496cb-8ad5-4&from=paste&height=523&id=uf4ef3273&originHeight=523&originWidth=1142&originalType=binary&ratio=1&rotation=0&showTitle=false&size=212070&status=done&style=none&taskId=uf0480458-5137-49c4-aff7-eed0c0ec989&title=&width=1142)
> 特点

Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部。
这一层就是我们通常说的容器曾，容器之下的都叫镜像层。
# commit镜像
```shell
docker commit 提交容器称为一个新的副本

# 命令和git类似
docker commit -m="提交的描述信息"-a="作者" 容器id 目标镜像名：[tag]
```
实战测试：
```shell
1、# 启动一个默认的tomcat

2、# 发现这个tomcat的webapps目录下没有任何东西

3、# 我们就需要将webapps.dist下的东西拷贝到webapps下

4、# 将我们的镜像提交（此时是一个新的镜像），我们以后就使用修改后的镜像即可
```
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682574586923-13e6c7d9-f962-47ed-a82a-4d65f261b2e1.png#averageHue=%230b0806&clientId=ue5b496cb-8ad5-4&from=paste&height=209&id=u0084854a&originHeight=209&originWidth=791&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18686&status=done&style=none&taskId=ud621dd3b-386d-452e-b6b9-76be725eed5&title=&width=791)
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35204765/1682574639391-cbaa382f-0cdc-42cc-9cd1-c83aca9e375f.png#averageHue=%230e0b08&clientId=ue5b496cb-8ad5-4&from=paste&height=213&id=ucbd31bd0&originHeight=213&originWidth=572&originalType=binary&ratio=1&rotation=0&showTitle=false&size=20794&status=done&style=none&taskId=u5150dfad-c268-449f-b21a-93e491b004d&title=&width=572)
如果你想保存当前容器的状态，就可以通过`commit`来提交，得到一个镜像。
