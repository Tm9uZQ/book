## 虚拟化技术

> Docker使用的是一种虚拟化技术。



- 虚拟化（英语：Virtualization）是一种**资源管理技术**，是将计算机的各种实体资源，如服务器、网络、内存及存储等，予以抽象、转换后呈现出来，**打破实体结构间的不可切割的障碍**。这些资源的新虚拟部分是不受现有资源的架设方式，地域或物理组态所限制。
- 虚拟化架构种类
  - 全虚拟化架构：全虚拟化技术最大特点就是可以虚拟出不同的操作系统。虚拟出来的每一个系统都有独立的系统文件。虚拟出来的操作系统可以与本机操作系统**不一样**(内核)。
    - VMware workstation
  - OS层虚拟化架构：虚拟出来的操作系统与本机操作系统**一样**(内核)。
    - Docker
  - 硬件层虚拟化架构：虚拟出来的操作系统是**没有宿主机**操作系统



## Docker

> Docker 是一个开源的**应用容器引擎**，基于 Go 语言开发。Docker 可以让开发者打包他们的应用以及相关依赖到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间完全隔离， 更重要的是容器**性能开销极低**
>
> 使用Docker可以实现开发人员的开发环境、测试人员的测试环境、运维人员的生产环境的一致性



### 组成结构

- 容器：一个独立的操作系统 + 目标软件
- 镜像：软件的安装包 + 软件的依赖环境
- 仓库：下载镜像的地方，公共仓库与私服仓库

|              名称               | 说明                                                         |
| :-----------------------------: | :----------------------------------------------------------- |
|      Docker 镜像 (Images)       | Docker 镜像是用于创建 Docker 容器的模板。镜像是基于联合文件系统的一种层式结构， 由一系列指令一步一步构建出来(只读不能修改)。 |
|     Docker 容器 (Container)     | 容器是独立运行的一个或一组应用。镜像相当于类，容器相当于类的对象，相当于操作系统 |
|      Docker 客户端(Client)      | Docker 客户端通过命令行或者其他工具使用 Docker API 与 Docker 的守护进程通信。 |
|        Docker 主机(Host)        | 一个物理或者虚拟的机器用于执行 Docker 守护进程和容器。       |
|         Docker守护进程          | 是Docker服务器端进程，负责支撑Docker 容器的运行以及镜像的管理。 |
| Docker 仓库 DockerHub(Registry) | Docker 仓库用来保存镜像，可以理解为代码控制中的代码仓库。 Docker Hub提供了庞大的镜像集合供使用。用户也可以将自己本地的镜像推送到Docker仓库供其他人下载。 |



### Docker安装

> 注意：建议安装在 CentOS7.x 以上的版本，在 CentOS6.x 的版本中，安装前需要安装其他很多的环境而且Docker很多补丁不支持更新。



- 在CentOS7中安装Docker的步骤

  ```shell
  # 1. yum 更新已有rpm包，升级linux内核(不做也可以)
  yum update
  
  # 2. 安装需要的软件包，yum-util提供yum-config-manager功能，另外两个是devicemapper驱动依赖
  yum install -y yum-utils device-mapper-persistent-data lvm2
  
  # 3. 设置yum源为阿里云
  yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  
  # 4. 安装docker【docker-ce: 社区版，免费；docker-ee：企业版，收费】
  yum install docker-ce -y
  
  # 5. 安装后查看docker版本
  docker -v
  ```



- 设置镜像源，创建并编辑文件/etc/docker/daemon.json

  ```shell
  # 执行如下命令
  mkdir /etc/docker
  vim /etc/docker/daemon.json  
  ```

  ```shell
  # 在文件中加入以下内容
  {
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
  }
  ```

  ```shell
  # 重启docker
  systemctl restart docker
  ```



### Docker相关命令

- 常用命令

  ```shell
  # 启动docker服务
  systemctl start docker
  # 停止docker服务
  systemctl stop docker
  # 重启docker服务
  systemctl restart docker
  # 查看docker服务状态
  systemctl status docker
  # 设置开机启动docker服务
  systemctl enable docker
  # 查看docker概要信息
  docker info
  # 查看docker帮助文档
  docker --help
  ```



- 镜像相关命令

  ```shell
  # 查看镜像
  docker images
  ```

  - REPOSITORY：镜像名称
  - TAG：镜像标签 ，镜像版本号 (latest代表是最新版，操作的时候可以省略版本号，其他的不准省略版本号)
  - IMAGE ID：镜像ID
  - CREATED：镜像的创建日期（不是获取该镜像的日期）
  - SIZE：镜像大小

  ```shell
  # 从网络中查找需要的镜像，也可以去官方搜索(http://hub.docker.com)
  docker search 镜像名称
  ```

  - NAME：镜像名称
  - DESCRIPTION：镜像描述
  - STARS：用户评价，反应一个镜像的受欢迎程度
  - OFFICIAL：是否官方
  - AUTOMATED：自动构建，表示该镜像由Docker Hub自动构建流程创建的

  ```shell
  # 拉取镜像就是从Docker仓库下载镜像到本地，镜像名称格式为 名称:版本号，如果版本号不指定则是最新的版本 命令如下:
  docker pull 镜像名称
  # 拉取centos 7
  docker pull centos:7
  # 拉取centos 最后版本镜像
  docker pull centos:latest
  ```

  ```shell
  # 按照镜像id删除镜像，或者镜像名称:版本号
  docker rmi 镜像ID|镜像的名称:版本号     
  
  # 删除所有镜像(谨慎操作)
  docker rmi `docker images -q`
  ```



- 容器相关命令

  ```shell
  # 查看正在运行容器
  docker ps
  # 查看所有容器
  docker ps -a 
  # 查看最后一次运行的容器
  docker ps –l
  ```



- 创建与运行容器

  - -i：表示运行容器
  - -t：表示容器启动后会进入其命令行。加入这两个参数后，容器创建就能登录进去。即分配一个伪终端/bin/bash
  - -d：在run后面加上-d参数, 则会创建一个守护式容器在后台运行（这样创建容器后不会自动登录容器，如果只加-i -t两个参数，并指定终端，创建后就会自动进去容器）
  - --name：为创建的容器命名(名称必须唯一)
  - -v：表示目录映射关系（前者是宿主机目录，后者是容器的目录），可以使用多个－v做多个目录或文件映射。注意：最好做目录映射，在宿主机上做修改，然后共享到容器上
  - -p：表示端口映射，前者是宿主机端口，后者是容器内的映射端口。可以使用多个-p做多个端口映射

  ```shell
  # 创建一个交互式容器【第一次启动后会直接进入当前容器，如果退出容器，则容器会变成停止状态】
  
  # 创建并启动名称为 mycentos7 的交互式容器
  # 容器名称 mycentos7
  # 镜像名称:TAG (centos:7)  也可以使用镜像id (5e35e350aded)
  # /bin/bash: 进入容器命令行
  docker run -it --name=mycentos7 centos:7 /bin/bash
  ```

  ```shell
  # 创建一个守护式容器【创建守护容器并不会马上进入到容器里面】
  
  # 创建并启动守护式容器
  # 容器名称: mycentos2
  # 镜像名称:TAG (centos:7)  也可以使用镜像id (5e35e350aded)
  docker run -id --name=mycentos2 centos:7  
  
  # 进入容器：
  # docker exec -it container_name (或者 container_id) /bin/bash
  # exit退出时，容器不会停止
  docker exec -it mycentos2 /bin/bash
  ```

  ```shell
  # 停止正在运行的容器: docker stop 容器名称|容器ID
  docker stop mycentos2
  
  # 启动已运行过的容器: docker start 容器名称|容器ID
  docker start mycentos2
  
  # 重启正在运行的容器: docker restart 容器名称|容器ID
  docker restart mycentos2
  ```



- 查看容器信息【docker容器内部的IP是有可能发生变化的】

  ```shell
  # 在linux宿主机下查看 mycentos2 的ip 
  # docker inspect 容器名称（容器ID）
  
  docker inspect mycentos2
  ```



- 删除容器【删除一个容器的前提是停止容器运行】

  ```shell
  # 删除指定的容器: docker rm 容器名称|容器ID
  docker rm mycentos2
  # 或者
  docker rm 2095a22bee70
  
  # 删除所有容器
  docker rm `docker ps -a -q`
  ```



- 容器文件拷贝

  ```shell
  # 将宿主机中的文件拷贝到容器内
  # docker cp 需要拷贝的文件或目录 容器名称:容器目录
  
  # 复制 abc.txt 到 mycentos2 的容器的 / 目录下 
  docker cp abc.txt mycentos2:/
  
  # 将文件从容器内拷贝出来到宿主机
  # docker cp 容器名称:文件路径 宿主机目标路径
  
  # 在Linux宿主机器执行复制；将容器mycentos2的/aaa.txt文件复制到 宿主机器的/root目录下
  docker cp mycentos2:/aaa.txt /root
  ```



- 容器目录挂载（目录映射）

  ```shell
  # 在创建容器时，将宿主机的目录与容器内的目录进行映射
  
  # 创建并启动容器mycentos3
  # 并挂载 linux中的/usr/local/test目录到容器的/usr/local/test
  # 也就是在 linux中的/usr/local/test中操作相当于对容器相应目录操作 
  docker run -id -v /usr/local/test:/usr/local/test --name=mycentos3 centos:7
  ```
  



### 创建常用应用容器

#### 安装MySQL

- -p 代表端口映射，格式为 宿主机映射端口:容器运行端口

- -e 代表添加环境变量 MYSQL_ROOT_PASSWORD 是root用户的远程登陆密码（如果是在容器中使用root登录的话,那么其**密码为空**）

  ```shell
  # 拉取MySQL 5.7镜像
  docker pull centos/mysql-57-centos7
  
  #把本地的mysql的服务停止，否则你没法使用3306端口去做映射(看你本地是否有安装mysql，如果没有安装可以省略该步骤)
  systemctl stop mysqld
  systemctl disable mysqld
  
  # 创建mysql5.7容器 
  docker runn -id --name=mysql5.7 -p 3306:33306 -e MYSQL_ROOT_PASSWORD=root centos/mysql-57-centos7
  
  # 进入mysql5.7容器
  docker exec -it mysql5.7 /bin/bash
  mysql -u root -p
  ```




#### 安装Tomcat

- 创建容器

  ```shell
  # 拉取tomcat镜像
  docker pull tomcat:8.5.71-jdk11-temurin-focal
  
  # 创建tomcat容器;并挂载了webapps目录，端口映射
  docker run -id --name=mytomcat -p 8080:8080 -v /usr/local/tomcat/webapps:/usr/local/tomcat/webapps tomcat:8.5.71-jdk11-temurin-focal
  ```

  

- 部署web应用

  ```shell
  #数据源配置
  spring:
    datasource:
      driver-class-name: com.mysql.jdbc.Driver
      #使用宿主机IP（已端口映射）
      url: jdbc:mysql://192.168.65.182:3306/springdb?useSSL=false
      username: root
      password: root
  mybatis:
    #别名扫描
    type-aliases-package: cn.itcast.model
    configuration:
      #开启下划线与小驼峰映射
      map-underscore-to-camel-case: true
      #开启mybatis执行sql语句的日志
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
     #定义mapper文件所在的位置
    mapper-locations:
      - classpath:mappers/*.xml
  ```

  ```xml
  - 修改springboot工程的pom文件
  <!--第一步，指定打war包-->
  <packaging>war</packaging>
    
    <!--第二步，打包排除内嵌tomcat-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <scope>provided</scope>
    </dependency>
    
    <!--第三步，指定springboot项目打包插件-->
    <build>
       <finalName>ROOT</finalName>
       <plugins>
           <plugin>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-maven-plugin</artifactId>
           </plugin>
       </plugins>
    </build>
  ```

  ```java
  /**
   * 编写WebServletInitializer类（Tomcat无法识别启动类，需要编写此类）
   */
  import cn.itcast.HighApplication;
  import org.springframework.boot.builder.SpringApplicationBuilder;
  import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
  
  /**
   * 作用等价于web.xml
   */
  public class WebServletInitializer extends SpringBootServletInitializer {
  
      @Override
      protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
          // 【注意】修改启动类
          builder.sources(Application.class);
          return builder;
      }
  }
  ```

  

- 进入项目pom文件命令行下，执行打包命令: mvn package -Dmaven.test.skip=true
- 上传ROOT.war到/usr/local/tomcat/webapps/目录下



#### 安装Nginx

- 创建容器

  ```shell
  # 拉取nginx镜像 
  docker pull nginx
  
  # 创建nginx容器和端口映射
  docker run -id --name=mynginx -p 80:80 nginx
  ```

- 配置nginx.conf反向代理设置

  ```
      server {
  		listen 80;
  		server_name 127.0.0.1;
  
  		location / {
  			proxy_pass http://宿主机ip地址:8080;
  		}
      }
  ```

- 重启nginx容器

  ```shell
  docker restart mynginx
  ```



#### 安装Redis

- 创建容器

  ```shell
  # 拉取redis镜像
  docker pull redis
  
  # 创建redis容器和端口映射
  docker run -di --name=myredis -p 6379:6379 redis
  ```



### 容器备份与迁移

> 主要作用：就是让配置好的容器，可以得到复用，后面用到得的时候就不需要重新配置

![1574231546282](images/1574231546282.png)



- 相关命令
  - 容器保存镜像: `docker commit 容器名称 新的镜像的名称`
  - 导出镜像: `docker save -o 镜像名称.tar 新的镜像的名称`
  - 导入镜像: `docker load -i 镜像名称.tar`



### registry私服仓库

> Docker官方的Docker hub（https://hub.docker.com）是一个用于管理公共镜像的仓库，我们可以从上面拉取镜像到本地，也可以把我们自己的镜像推送上去。但是，有时候我们的服务器无法访问互联网，或者你不希望将自己的镜像放到公网当中，那么我们就需要搭建自己的私有仓库来存储和管理自己的镜像。



#### 搭建私服

```shell
# 1、拉取私有仓库镜像 
docker pull registry

# 2、启动私有仓库容器
docker run -di --name=registry -p 5000:5000 registry 

# 3、打开浏览器 输入地址http://宿主机ip:5000/v2/_catalog，看到{"repositories":[]} 表示私有仓库 搭建成功。
```



#### 将镜像上传至私服

```shell
# 假设现在是另一台服务器，修改daemon.json
vim /etc/docker/daemon.json

# 在上述文件中添加一个key，保存退出。此步用于让 docker 信任私有仓库地址
{
"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"],
"insecure-registries":["私服地址:5000"]
}

# 标记镜像为私有仓库的镜像 
# 语法: docker tag 镜像名称 私服IP:5000/镜像名称
docker tag jdk1.8 192.168.12.132:5000/jdk1.8


# 上传标记的镜像到私有仓库
# 语法: docker push 宿主机IP:5000/镜像名称
docker push 192.168.12.132:5000/jdk1.8

# 3、输入网址查看仓库效果
```



#### 从私服拉取镜像

```shell
# 拉取镜像 
# 语法: docker pull 服务器ip:5000/jdk1.8
docker pull 192.168.12.132:5000/jdk1.8

#可以通过如下命令查看 docker 的信息；了解到私有仓库地址 
docker info
```



### Dockerfile构建镜像（了解）

+ 那如果我们想自己开发一个镜像，那该如何做呢？答案是: Dockerfile 
+ Dockerfile其实就是一个文本文件，由一系列命令和参数构成，Docker可以读取Dockerfile文件并根据Dockerfile文件的描述来构建镜像。
+ Dockerfile文件内容一般分为4部分
  + 基础镜像信息（镜像 = 软件 + 软件运行环境）
  + 维护者信息（作者）
  + 镜像操作指令  
  + 容器启动时执行的指令



#### 常用命令

| **命令**                            | **作用**                                                     |
| ----------------------------------- | ------------------------------------------------------------ |
| FROM image_name:tag                 | 定义了使用哪个基础镜像启动构建流程                           |
| MAINTAINER user_name                | 声明镜像的创建者                                             |
| ENV key value                       | 设置环境变量 (可以写多条)                                    |
| RUN command                         | 是Dockerfile的核心部分(可以写多条)                           |
| ADD source_dir/file  dest_dir/file  | 将宿主机的文件复制到镜像创建的容器内，如果是一个压缩文件，将会在复制后自动解压 |
| COPY source_dir/file  dest_dir/file | 和ADD相似，但是如果有压缩文件并不能解压                      |
| WORKDIR path_dir                    | 设置工作目录（别人一进去到你容器所在路径）                   |



#### 构建镜像

```shell
# 1、创建目录 
mkdir /usr/local/dockerjdk8 
cd /usr/local/dockerjdk8 

# 2、下载jdk-8u171-linux-x64.tar.gz并上传到服务器（虚拟机）中的/usr/local/dockerjdk8目录 

# 3、在/usr/local/dockerjdk8目录下创建Dockerfile文件，文件内容如下:
vim Dockerfile

FROM centos:7
MAINTAINER ztl
WORKDIR /usr
RUN mkdir /usr/local/java
ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV PATH $JAVA_HOME/bin:$PATH

# 4、执行命令构建镜像；不要忘了后面的那个 . （点代表当前目录）
# -t 指定镜像名称
docker build -t='jdk1.8' .

# 5、查看镜像是否建立完成 
docker images
```



#### 根据镜像创建容器

```shell
# 创建并启动容器 
docker run -it --name=testjdk jdk1.8 /bin/bash

# 在容器中测试jdk是否已经安装 
java -version
```



