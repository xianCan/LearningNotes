# Maven学习笔记

## 第一章  Maven的认识

### 1.1  目前开发中存在的问题

* 一个项目就是一个工程
* 项目中需要的jar包必须手动复制到相应的/lib目录下
* jar包需要别人替我们准备好，或者自己下载
* 一个jar包依赖的其他jar包需要自己手动加到项目中

### 1.2  Maven的概念

* **Maven是一款服务于Java平台的自动化构建工具**  

  Make --> Ant --> Maven --> Gradle

* **构建**  

  概念：以Java源文件、框架配置文件、前端源文件、静态资源等为原材料，生产一个可以运行的项目的过程  

### 1.3  构建过程

* 清理：将以前编译得到的旧的class字节码文件删除，为下一次编译做准备  
* 编译：将Java源代码编译成class字节码文件  
* 测试：自动测试，自动调用juint程序  
* 报告：测试程序执行的结果  
* 打包：动态web工程打war包，Java工程打jar包  
* 安装：Maven特定概念——将打包得到的文件复制到“仓库”中的指定位置  
* 部署：将动态Web工程生成的war包复制到Servlet容器的指定目录下，使其可以运行

### 1.4  Maven的安装

* 解压即可，建议路径不要包含中文

* **配置Maven环境变量：**

  * **提示：**  

    **环境变量的HOME目录通常为bin目录的上一级，而Path目录通常就是到bin目录。**

## 第二章  Maven的核心概念

* 核心概念：
  * **约定的目录结构**
  * **POM**
  * **坐标**
  * **依赖**
  * 仓库
  * 生命周期/插件/目标
  * 继承
  * 聚合

### 2.1  约定的目录结构

* 项目名

  * src：源码
    * main：主程序目录
      * java：存放Java源文件
      * resources：存放配置文件
    * test：测试主目录
      * java：存放测试的Java源文件
      * resources：存放测试的配置文件

  * pom.xml：Maven工程的核心配置文件

* **为什么要遵守约定的目录结构？**

  * Maven要负责我们这个项目的自动化构建，以编译为例，Maven要想自动进行编译，那么它必须知道Java    

    源文件保存在哪里

  * 如果我们自定义的东西要让框架或工具知道，有两种办法：

    * 以配置的方式明确告诉框架，配置文件/注解
    * 遵守框架内部已经存在的约定：约定 > 配置 > 编码

### 2.2  Maven仓库问题

* Maven的核心程序中仅仅等一了抽象的生命周期，但是具体的工作必须有特定的插件来完成。而插件本身并  

  不包含在Maven的核心程序中。

* 当我们执行的Maven命令需要用到某些插件时，Maven核心程序会首先到本地仓库中查找。

* 本地仓库的默认位置：HOME目录/.m2/repository。

* Maven核心程序如果在本地仓库中找不到需要的插件，那么他会自动连接外网，到中央仓库中下载。

* 因此通常情况下需要指定本地仓库和中央仓库

  * 本地仓库在settings.xml中的localRepository标签中修改
  * 在服务器时，通常会将中央仓库修改为自己搭建的私服

### 2.3  常用Maven命令

* mvn clean
* mvn compile
* mvn package
* mvn test
* mvn verify：检查
* mvn install：安装到本地仓库
* mvn deploy：发布到远程仓库

### 2.4 POM

* **Project Object Model  项目对象模型**

* pom.xml对于Maven工程师核心配置文件，与工件过程相关的一切设置都在这个文件中进行配置

### 2.5  坐标

**使用三个两在仓库中唯一定位一个Maven工程**  

* groupid：公司或组织域名倒序+项目名
* artifactid：模块名
* version：版本名

**Maven工程坐标与仓库中路径的对应关系**

```xml
<groupId>org.springframework</groupId>
<artifactId>spring-core</artifactId>
<version>4.0.0.RELEASE</version>

#具体包路径
org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE
```

### 2.6  仓库

* **本地仓库**：当前电脑上部署的仓库目录，为当前电脑上所有Maven工程服务
* **远程仓库**：
  * 私服：Nexus搭建在局域网环境中，为局域网范围内的所有Maven工程服务
  * 中央仓库：架设在Internet上，为全世界所有Maven工程服务
  * 中央仓库镜像：为了分担中央仓库的压力，提升用户访问速度，架设在各个地方
* **仓库中保存的内容**：
  * Maven自身所需要的插件
  * 第三方框架或工具的jar包
  * 自己开发的Maven工程

### 2.7  依赖

* Maven解析依赖信息时回到本地仓库中查找被依赖的jar包
  * 对于我们自己开发的Maven工程，使用mvn install命令安装后就可以进入仓库

* 依赖的范围<scope></scope>  

  * compile范围依赖
    * 对主程序是否有效：有效
    * 对测试程序是否有效：有效
    * 是否参与打包：参与

  * test范围依赖
    * 对主程序是否有效：无效
    * 对测试程序是否有效：有效
    * 是否参与打包：不参与
  * provided范围依赖
    * 对主程序是否有效：有效
    * 对测试程序是否有效：有效
    * 是否参与打包：不 参与

### 2.8  生命周期

* 各个构件环节执行的顺序：不能打乱顺序，必须按照既定的正确顺序来执行

* Maven的黑心程序中定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的

* Maven核心程序为了更好的实现自动化构建，按照这一特点执行生命周期中的各个阶段：不论现在要执行生  

  命周期中的哪一个阶段，都是从这个生命周期最初的位置开始执行

### 2.9  依赖的传递性

* 可以传递的依赖在底层维护一份即可，test和provided范围的依赖不能传递

### 2.10  依赖的排除

* 和依赖的传递性属于相反的操作，将某一个底层传递过来的依赖排除掉

  ```xml
  <exclusions>
  	<exclusion>
  		<groupId></groupId>
  		<artifactId></artifactId>
  	</exclusion>
  </exclusions>
  ```


### 2.11  依赖的原则

* 解决模块工程之间的jar包冲突问题，有如下两个原则：
  * 路径最短者优先
  * 路径一样时，先声明者优先

### 2.12  统一管理依赖的版本

* 如果spring各个jar包的依赖版本都是4.0.0，需要统一升级为4.1.1，使用properties标签内使用自动以标签统  

  一声明版本号，在需要同一版本的位置，使用${自定义标签名}引用声明的版本号

### 2.13  插件

* springboot项目中常用的打包插件，当运行package进行打包时，会自动打成一个可以直接运行的JAR文件，  

  使用"java -jar"命令就可以直接运行

  ```xml
  <plugins>
      <plugin>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-maven-plugin</artifactId>
          <configuration>
              <source>1.8</source>
              <target>1.8</target>
          </configuration>
      </plugin>
  </plugins>
  ```

### 2.14  查找依赖信息的网站

```
http://mvnrepository.com/
```

