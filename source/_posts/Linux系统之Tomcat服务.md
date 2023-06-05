---
title: Linux系统之Tomcat服务
date: 2023-02-12 13:38:27
tags: [Linux,Tomcat,运维]
---

## 环境介绍
操作系统：Ubuntu 20.04.2 LTS
JDK版本：JDK 7.0
Tomcat版本：Tomcat 8.0

## 概念科普

### JavaWeb
Java web，是用java技术来解决相关web互联网领域的技术的总称。web包括：web服务器和web客户端两部分。
java在最早web客户端的应用有java applet程序，不过这种技术在很久之前就已经被淘汰了。java在服务器端的应用非常丰富，
比如Servlet，JSP（JavaServer Pages）和第三方框架等等。java技术对web领域的发展注入了强大的动力
<!--more-->
### web资源分类
所谓web资源即放在Internet网上供外界访问的文件或程序，又根据它们呈现的效果及原理不同，将它们划分为静态资源和动态资源。

静态web资源：固定不变的数据文件（静态网页HTML、CSS文件、文本、音频、视频）
静态web技术：HTML+CSS+JavaScript

动态web资源：一段服务程序，运行后，生成的数据文件
动态web技术：servlet，jsp，php， .net，ruby，python等等

### 常见的web服务器
Tomcat：由Apache组织提供的一种Web服务器，提供对jsp和Servlet的支持。它是一种轻量级的JavaWeb容器（服务器），也是当前应用最广的JavaWeb服务器（免费）。
Jboss：是一个遵从JavaEE规范的、开放源代码的、纯Java的EJB服务器，它支持所有的JavaEE规范（免费）。
GlassFish： 由Oracle公司开发的一款JavaWeb服务器，是一款强健的商业服务器，达到产品级质量（应用很少，收费）。
Resin：是CAUCHO公司的产品，是一个非常流行的应用服务器，对servlet和JSP提供了良好的支持，性能也比较优良，resin自身采用JAVA语言开发（收费，应用比较多）。
WebLogic：是Oracle公司的产品，是目前应用最广泛的Web服务器，支持JavaEE规范，而且不断的完善以适应新的开发要求，适合大型项目（收费，用的不多，适合大公司）。

## 一、Tomcat简介
开源小型web服务器 ，完全免费，主要用于中小型web项目，只支持Servlet和JSP 等少量javaee规范（就是JavaWeb编程接口）
servlet：sun公司提供的用于开发动态web资源的技术。
jsp：（java server page），java提供的一门开发web网页的技术。
tomcat软件：java开发的。java软件运行的时候需要jdk。

| Tomcat版本 | Servlet/JSP版本 | JavaEE版本 | 运行环境 |
| :-- | :-- | :-- | :-- |
|4.1|2.3/1.2|1.3|JDK1.3|
|5.0|2.4/2.0|1.4|JDK1.4|
|5.5/6.0|2.5/2.1|5.0|JDK5.0|
|7.0|3.0/2.2|6.0|JDK6.0|
|8.0|3.1/2.3|7.0|JDK7.0|

## 二、Tomcat下载

访问http://tomcat.apache.org
下载对应Linux的版本apache-tomcat-8.5.76.tar.gz
访问http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html
下载JDK7.0

## 三、Tomcat & JDK安装
将apache-tomcat-8.5.76.tar.gz拷贝到/opt/mytomcat并解压
将jdk-7u80-linux-x64.tar.gz拷贝到/opt/jvm并解压
```bash
tar -zxvf apache-tomcat-8.5.76.tar.gz
tar -zxvf jdk-7u80-linux-x64.tar.gz
```

配置环境变量

```bash
vim /etc/profile
#在文件末尾加
export JAVA_HOME=/opt/jvm/jdk1.7.0_80
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```
保存后，需要使配置生效，使用命令
```bash
source /etc/profile
java -version #验证成功
```
配置Tomcat的Java环境
```bash
vim /opt/mytomcat/apache-tomcat-8.5.76/bin/startup.sh
#在**最后一行之前**加上
#set java environment
export JAVA_HOME=/opt/jvm/jdk1.7.0_80
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
#tomcat
export TOMCAT=/opt/mytomcat/apache-tomcat-8.5.76
#注意关闭Tomcat同样需要配置shutdown.sh,将上述配置同样放在最后一行之前
```
验证打开浏览器，访问http://localhost:8080 看到页面就算成功了

## 四、Tomcat目录介绍

### 1、tomcat目录

|目录|介绍|
|-|-|
|bin|可执行文件|
|conf|Tomcat服务器配置文件|
|lib|Tomcat启动后所依赖的jar包|
|logs|Tomcat工作之后的日志文件|
|temp|缓存|
|webapps|Tomcat部署工程的目录|
|work|JSP文件在被翻译之后，保存在当前这个目录下，session对象被序列化之后保存的位置|

### 2、webapps目录

|目录|介绍|
|-|-|
|doc|Tomcat帮助文档|
|examples|web应用实例|
|host-manager|主机管理|
|manager|管理|
|ROOT|默认站点根目录|

### 3、conf目录

|目录|介绍|
|-|-|
|catalina||
|catalina.policy|                        |
|catalina.properties|                        |
|context.xml|                        |
|logging.properties|                        |
|logs||
|server.xml|tomcat主配置文件|
|tomcat-users.xml|tomcat管理用户配置文件Tomcat访问用户信息及角色信息|
|tomcat-users.xsd||
|web.xml|Tomcat应用配置文件|

## 五、Tomcat运维

### 1、Tomcat的启动与停止
Windows startup.bat
linux startup.sh

### 解决Tomcat启动慢的方法
启动慢的原因是session引起的随机数问题导致的。Tomcat的Session ID是通过SHA1算法计算得到的，计算Session ID的时候必须有一个密钥。为了提高安全性Tomcat在启动的时候会通过随机数产生一个密钥。启动慢的主要原因是生产随机数的时候卡住了，导致tomcat启动不了。
是否有足够的熵来用于产生随机数，可以通过如下命令来查看
```bash
cat /proc/sys/kernel/random/entropy_avail
```
为了加速/dev/random提供随机数的速度，你可以通过操作设备的外设，让其产生大量的中断，网络传输数据，移动鼠标，在命令行敲几个不同的命令，俗称聚气
#### 方法1
```bash
vim $JAVA_HOME/jre/lib/security/java.security
securerandom.source=file:/dev/random
```
#### 方法2
```bash
vim $TOMCAT_HOME/bin/catalina.sh
if [["JAVA_OPTS" != *-Djava.security.egd=* ]]; then
JAVA_OPTS="$JAVA_OPTS - Djava.security.egd=file:/dev/urandom"
fi
```
这个系统属性egd表示熵收集守护进程（entropy gathering daemon）
#### 方法3
```bash
yum install rng-tools # 安装rngd服务（熵服务，增大熵池）
systemctl start rngd # 启动服务
```

### 2、Tomcat管理功能（注意：测试功能，生产环境不要使用）
待补充

### 3、Tomcat日志文件详解
**catalina.out**
catalina.out即标准输出和标准出错，所有输出到这两个位置的都会进入catalina.out，这里包含tomcat运行自己输出的日志以及应用里向console输出的日志。默认这个日志文件是不会进行自动切割的，我们需要借助其他工具进行切割（注意：catalina.out文件如果过大会影响）
**catalina.YYYY-MM-DD.log**
catalina.{yyyy-MM-dd}.log是tomcat自己运行的一些日志，这些日志还会输出到catalina.out，但是应用向console输出的日志不会输出到catalina.{yyyy-MM-dd}.log,它是tomcat的启动和暂停时的运行日志，注意，它和catalina.out是里面的内容是不一样的。
**localhost.YYYY-MM-DD.log**
localhost.{yyyy-MM-dd}.log主要是应用初始化(listener, filter, servlet)未处理的异常最后被tomcat捕获而输出的日志,它也是包含tomcat的启动和暂停时的运行日志,但它没有catalina.YYYY-MM-DD.log日志全。它只是记录了部分日志。
**localhost_access_log.YYYY-MM-DD.txt**
localhost_access_log.YYYY-MM-DD.txt是访问tomcat的日志，发起请求的IP，时间，资源，状态码都有记录。
**host-manager.YYYY-MM-DD.log**
host-manager.2018-09-19.log：这个估计是放tomcat的自带的manager项目的日志信息的，未看到有什么重要的日志信息。
**manager-YYYY-MM-DD.log**
manager.2018-09-19.log ：这个是tomcat manager项目专有的日志文件

### 4、Tomcat主配置文件详解
tomcat目录下的/conf/server.xml

#### 1、server.xml组件类别
顶级组件：位于整个配置的顶层，如server
容器类组件：可以包含其他组件的组件，如service、engine、host、context
连接器组件：连接用户请求至tomcat，如connector
被嵌套类组件：位于一个容器当中，不能包含其他组件，如Valve、logger
```
<server>
	<service>
	<connector />
	<engine>
	<host>
	<context></context>
	</host>
	<host>
	<context></context>
	</host>
	</engine>
	</service>
</server>
```
#### 2、组件介绍

|  组件名称  | 功能介绍 |
|  ----  | ----  |
| engine | 核心容器组件，catalina引擎，负责通过connector接收用户请求，并处理请求，将请求转至对应的虚拟主机host。 |
| host | 类似于httpd中的虚拟主机，一般而言支持基于FQDN的虚拟主机。 |
| context | 定义一个应用程序，是一个最内层的容器类组件（不能再嵌套）。配置context的主要目的指定对应对的webapp的根目录，类似于httpd的alias，其还能为webapp指定额外的属性，如部署方式等。 |
| connector | 接收用户请求，类似于httpd的listen配置监听端口的 |
| service | 将connector关联至engine，因此一个service内部可以有多个connector，但只能有一个引擎engine。service内部有两个connector，一个engine。因此，一般情况下一个server内部只有一个service，一个service内部只有一个engine，但一个service内部可以有多个connector。 |
| server | 表示一个运行于JVM中的tomcat实例。 |
| Valve | 阀门，拦截请求并在将其转至对应的webapp前进行某种处理操作，可以用于任何容器中，比如记录日志（access log valve）、基于IP做访问控制（remote address filter valve）。 |
| logger | 日志记录器，用于记录组件内部的状态信息，可以用于除context外的任何容器中。 |
| realm | 可以用于任意容器类的组件中，关联一个用户认证库，实现认证和授权。可以关联的认证库有两种：UserDataBaseRealm、MemoryRealm和DBCRealm。 |
| UserDataBaseRealm | 使用JNDI自定义的用户认证库。 |
| MemoryRealm | 认证信息定义在tomcat-users.xml中。 |
| JDBCRealm | 认证信息定义在数据库中，并通过JDBC连接至数据库中查找认证用户。 |

#### 3、server.xml配置文件注释
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--
  Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<!-- Note:  A "Server" is not itself a "Container", so you may not
     define subcomponents such as "Valves" at this level.
     Documentation at /docs/config/server.html
 -->
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <!-- Security listener. Documentation at /docs/config/listeners.html
  <Listener className="org.apache.catalina.security.SecurityListener" />
  -->
  <!-- APR library loader. Documentation at /docs/apr.html -->
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <!-- Prevent memory leaks due to use of particular java/javax APIs-->
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <!-- Global JNDI resources
       Documentation at /docs/jndi-resources-howto.html
  -->
  <GlobalNamingResources>
    <!-- Editable user database that can also be used by
         UserDatabaseRealm to authenticate users
    -->
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <!-- A "Service" is a collection of one or more "Connectors" that share
       a single "Container" Note:  A "Service" is not itself a "Container",
       so you may not define subcomponents such as "Valves" at this level.
       Documentation at /docs/config/service.html
   -->
  <Service name="Catalina">

    <!--The connectors can use a shared executor, you can define one or more named thread pools-->
    <!--
    <Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="150" minSpareThreads="4"/>
    -->


    <!-- A "Connector" represents an endpoint by which requests are received
         and responses are returned. Documentation at :
         Java HTTP Connector: /docs/config/http.html
         Java AJP  Connector: /docs/config/ajp.html
         APR (HTTP/AJP) Connector: /docs/apr.html
         Define a non-SSL/TLS HTTP/1.1 Connector on port 8080
    -->
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    <!-- A "Connector" using the shared thread pool-->
    <!--
    <Connector executor="tomcatThreadPool"
               port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    -->
    <!-- Define an SSL/TLS HTTP/1.1 Connector on port 8443
         This connector uses the NIO implementation. The default
         SSLImplementation will depend on the presence of the APR/native
         library and the useOpenSSL attribute of the AprLifecycleListener.
         Either JSSE or OpenSSL style configuration may be used regardless of
         the SSLImplementation selected. JSSE style configuration is used below.
    -->
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
               maxThreads="150" SSLEnabled="true">
        <SSLHostConfig>
            <Certificate certificateKeystoreFile="conf/localhost-rsa.jks"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>
    -->
    <!-- Define an SSL/TLS HTTP/1.1 Connector on port 8443 with HTTP/2
         This connector uses the APR/native implementation which always uses
         OpenSSL for TLS.
         Either JSSE or OpenSSL style configuration may be used. OpenSSL style
         configuration is used below.
    -->
    <!--
    <Connector port="8443" protocol="org.apache.coyote.http11.Http11AprProtocol"
               maxThreads="150" SSLEnabled="true" >
        <UpgradeProtocol className="org.apache.coyote.http2.Http2Protocol" />
        <SSLHostConfig>
            <Certificate certificateKeyFile="conf/localhost-rsa-key.pem"
                         certificateFile="conf/localhost-rsa-cert.pem"
                         certificateChainFile="conf/localhost-rsa-chain.pem"
                         type="RSA" />
        </SSLHostConfig>
    </Connector>
    -->

    <!-- Define an AJP 1.3 Connector on port 8009 -->
    <!--
    <Connector protocol="AJP/1.3"
               address="::1"
               port="8009"
               redirectPort="8443" />
    -->

    <!-- An Engine represents the entry point (within Catalina) that processes
         every request.  The Engine implementation for Tomcat stand alone
         analyzes the HTTP headers included with the request, and passes them
         on to the appropriate Host (virtual host).
         Documentation at /docs/config/engine.html -->

    <!-- You should set jvmRoute to support load-balancing via AJP ie :
    <Engine name="Catalina" defaultHost="localhost" jvmRoute="jvm1">
    -->
    <Engine name="Catalina" defaultHost="localhost">

      <!--For clustering, please take a look at documentation at:
          /docs/cluster-howto.html  (simple how to)
          /docs/config/cluster.html (reference documentation) -->
      <!--
      <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"/>
      -->

      <!-- Use the LockOutRealm to prevent attempts to guess user passwords
           via a brute-force attack -->
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <!-- This Realm uses the UserDatabase configured in the global JNDI
             resources under the key "UserDatabase".  Any edits
             that are performed against this UserDatabase are immediately
             available for use by the Realm.  -->
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>

      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">

        <!-- SingleSignOn valve, share authentication between web applications
             Documentation at: /docs/config/valve.html -->
        <!--
        <Valve className="org.apache.catalina.authenticator.SingleSignOn" />
        -->

        <!-- Access log processes all example.
             Documentation at: /docs/config/valve.html
             Note: The pattern used is equivalent to using pattern="common" -->
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />

      </Host>
    </Engine>
  </Service>
</Server>
```
#### 4、Connector主要参数说明

| 参数 | 参数说明 |
| --- | --- |
| connector | 接收用户请求，类似于httpd的listen配置监听端口 |
| port | 指定服务器端要创建的端口号，并在这个端口监听来自客户端的请求。 |
| address | 指定连接器监听的地址，默认为所有地址（即0.0.0.0） |
| protocol | 连接器使用的协议，支持HTTP和AJP。AJP（Apache Jserv Protocol）专用于tomcat于apache建立通信的，在httpd反向代理用户请求至tomcat时使用（可见Nginx反向代理时不可用AJP协议）。 |
| minProcessors | 服务器启动时创建的处理请求的线程数 |
| mxProcessors | 最大可以创建的处理请求的线程数 |
| enableLookups | 如果为true，则可以通过调用request.getRemoteHost()进行DNS查询来得到远程客户端的实际主机名，若为false则不进行DNS查询，而是返回其IP地址 |
| redirectPort | 指定服务器正在处理http请求时收到了一个SSL传输请求后重定向的端口号 |
| acceptCount | 指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理 |
| connectionTimeout | 指定超时的时间数（以毫秒为单位） |

#### 5、host参数详解

| 参数 | 参数说明 |
| --- | --- |
| host | 表示一个虚拟主机 |
| name | 指定主机名 |
| appBase | 应用程序基本目录，即存放应用程序的目录，一般为appBase="webapps"相对于CATALINA_HOME而言的，也可以写绝对路径 |
| unpackWARs | 如果为true，则tomcat会自动将WAR文件解压，否则不解压，直接从WAR文件中运行应用程序 |
| autoDeploy | 在tomcat启动时，是否自动部署 |
| xmlValidation | 是否启动xml的校验功能，一般xmlValidation="false"。 |
| xmlNamespaceAware | 检测名称空间，一般xmlNamespaceAware="false"。 |

#### 6、Context参数说明

| 参数 | 参数说明 |
| --- | --- |
| Context | 表示一个web应用程序，通常为WAR文件 |
| docBase | 应用程序的路径或者是WAR文件存放的路径，也可以使用相对路径，其实路径为此Context所属Host中appBase定义的路径。 |
| path | 表示此web应用程序的url的前缀，这样请求的url为http://localhost:8080/path/ |
| reloadable | 这个属性非常重要，如果为true则tomcat会自动检测应用程序的/WEB-INF/lib和/WEB-INF/classes目录的变化，自动装载新的引用程序，可以在不重启tomcat的情况下改变应用程序 |

### 5、WEB站点部署
1、直接将程序目录放在webapps目录下
2、使用开发工具将程序打包成war包，然后上传到webapps目录下
#### 1、自定义默认网站目录
上面访问的网址为http://ip:port/memtest/meminfo.jsp
现在想要访问格式为http://ip:port/meminfo.jsp
方法1
```
/application/tomcat/bin/shutdown.sh
mkdir ROOT
cp -r memtest/* ROOT/*
/application/tomcat/bin/startup.sh
```
方法2
```
vim /application/tomcat/conf/server.xml +150
#添加上这一行
<Context path=""
docBase="/application/tomcat/webapps/test" debug="0"
reloadable="false" crossContext="true"/>

rm -rf memtest memtest.war
mkdir test
cp -r ROOT/* test/
rm -rf ROOT/
```
修改配置文件后要重启服务
```
/application/tomcat/bin/shutdown.sh
/application/tomcat/bin/startup.sh
```
#### 2、部署开源站点jpress
<待补充>

## 六、Tomcat优化
### 1、tomcat安全优化
#### 1、telent管理端口保护（强制）

| 类别 | 配置内容及说明 | 标准配置 |
| --- | --- | --- |
| telnet管理端口保护 | 1.修改默认的8005管理端口为不易猜测的端口（大于1024）；2.修改SHUTDOWN指令为其他字符串 | <Server port="8527" shutdown="dangerous"> |

#### 2、ajp连接端口保护（推荐）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| ---- | --- |--- | --- |
| Ajp连接端口保护 | 1.修改默认的ajp8009端口为不易冲突的大于1024端口；2.通过iptables规则限制ajp端口访问的权限仅为线上机器 | <Connector prt="8528" protocol="AJP/1.3"/> | 以上配置项的配置内容仅为建议配置，请按照服务实际情况进行合理配置，但要求端口配置在8000-8999之间；保护此端口的目的在于防止线下的测试流量被mod_jk转发至线上tomcat服务器； |

#### 3、禁用管理端（强制）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| --- | --- | --- | --- |
| 禁用管理端 | 1.删除默认的tomcat安装目录/conf/tomcat-users.xml文件重启tomcat后将会自动生成新的文件；2.删除tomcat安装目录/webapps下默认的所有目录和文件；3.将tomcat应用根目录配置为tomcat安装目录以外的目录 | <Host name>="localhost" appBase="/application/work/webapps" | 对于前段web模块，tomcat管理端属于tomcat的高危安全隐患，一旦被攻破，黑客通过上传webshell的方式将会直接取得服务器的控制权，后果极其严重； |

#### 4、降权启动（强制）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| --- | --- | --- | --- |
| 降权启动 | 1.tomcat启动用户权限必须为非root权限，尽量降低tomcat启动用户的目录访问权限；2.如需直接对外使用80端口，可通过普通账号启动后，配置iptables规则进行转发； |  | 避免一旦tomcat服务被入侵，黑客直接获取高级用户权限危害整个server的安全 |

```
useradd tomcat
mkdir /home/tomcat -p
cp -a /application/tomcat /home/tomcat/tomcat8_1
chown -R tomcat:tomcat /home/tomcat/tomcat8_1/ #这里的tomcat:tomcat均指的是新添加的用户名
su -c '/home/tomcat/tomcat8_1/bin/startup.sh' tomcat
ps -ef |grep tomcat #验证是否为tomcat用户启用了进程
```

#### 5、文件列表访问控制（强制）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| --- | --- | --- | --- |
| 文件列表访问控制 | 1.conf/web.xml文件中default部分listings的配置必须为false； | <init-param><param-name>listings</param-name><param-value>false</param-value></init-param> | false为布列出目录文件，true为允许列出，默认为false |

#### 6、版本信息隐藏（强制）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| --- | --- | --- | --- |
| 版本信息隐藏 | 1.修改conf/web.xml,重定向403、404以及500等错误到指定的错误页面；2.也可以通过修改应用程序目录下的WEB-INF/web.xml下的配置进行错误页面的重定向； | <error-page><error-code>**403**</error-code><location>**/forbidden.jsp**</location></error-page><error-page><error-code>**404**</error-code><location>**/notfound.jsp**</location></error-page><error-page><error-code>**500**</error-code><location>**/systembusy.jsp**</location></error-page> | 在配置中对一些常见错误进行重定向，避免当出现错误时tomcat默认显示的错误页面暴露服务器和版本信息；必须确保程序根目录下的错误页面已经存在； |

#### 7、Server header重写（推荐）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| --- | --- | --- | --- |
| Serverheader重写 | 在HTTPConnector配置中加入server的配置； | server="webserver" | 当tomcat HTTP端口直接提供web服务时此配置生效，加入此配置，将会替换http响应Serverheader部分的默认配置，默认是Apache-Coyote/1.1 |

#### 8、访问限制（可选）

| 类别 | 配置内容及说明 | 标准配置 | 备注 |
| --- | --- | --- | --- |
| 访问限制 | 通过配置，限定访问的IP来源 | <Context path="" docBase="/home/work/tomcat" debug="0" reloadable="false" crossContext="true"><Valve className="org.apache.catalina.valves.RemoteAddrValve"**allow="61.148.18.138,61.135.165.130" deny="*.*.*.*"**/></Context> | 通过配置信任ip的白名单，拒绝非白名单IP的访问，此配置主要针对高保密级别的系统，一般产品线不需要 |

#### 9、启停脚本权限回收（推荐）

| 类别 | 配置内容及说明 | 标准配置或操作 | 备注 |
| --- | --- | --- | --- |
| 启停脚本权限回收 | 去除其他用户对tomcat的bin目录下shutdown.sh、startup.sh、catalina.sh的可执行权限； | chmod -R 744 tomcat/bin/* | 防止其他用户有启停线上tomcat的权限； |

#### 10、访问日志格式规范（推荐）

| 类别 | 配置内容及说明 | 标准配置或操作 | 备注 |
| --- | --- | --- | --- |
| 访问日志格式规范 | 开启tomcat默认访问日志中的Referer和User-Agent记录 | <Valve className="org.apache.caalina.valves.AccessLogValve"directory="logs" prefix="localhost_access_log." suffix=".txt"   pattern="%h %1 %u %t %r %s %b %{Referer}i %{User-Agent}i %D" resolveHosts="false" /> | 开启Referer和User-Agent是为了一旦出现安全问题能够更好的根据日志进行问题排查； |

#### 11、附录：建议配置及标准执行方案

1、配置部分（${CATALINA_HOME}conf/server.xml）
```xml
<Server port="8527">

<!-- Define a non-ssl HTTP/1.1 Connector on port 8080 -->
<Connector port="8080" server="webserver"/>

<!-- Define an AJP 1.3 Connector on port 8080 -->
<!-- Define an accesslog -->
<Valve className="org.apache.catalina.valves.AccessLogValve"
  directory="logs" prefix="localhost_access_log." suffix=".txt"
  pattern="%h %1 %u %t %r %s %b %{Referer}i %{User-Agent}i %D"
resolveHosts="false"/>

<Connector port="8528" protocol="AJP/1.3" />
```
2、配置部分（${CATALINA_HOME}conf/web.xml或者WEB-INF/web.xml）
```xml
vim /application/tomcat/conf/web.xml

<init-param>
  <param-name>listings</param-name>
  <param-value>false</param-value>
</init-param>

在</web-app>标签前面添加：
<error-page>
  <error-code>403</error-code>
  <location>/forbidden.jsp</location>
</error-page>
<error-page>
  <error-code>404</error-code>
  <location>/notfound.jsp</location>
</error-page>
<error-page>
  <error-code>500</error-code>
  <location>/systembusy.jsp</location>
</error-page>
```
3、删除如下tomcat的默认目录和默认文件
```
tomcat/webapps/*
tomcat/conf/tomcat-user.xml
```
4、去除其他用户对tomcat启停脚本的执行权限
```
chmod 744 -R tomcat/bin/*
```

### 2、tomcat性能优化
#### 1、上策：优化代码
该项需要开发经验足够丰富，对开发人员要求较高
#### 2、中策：jvm优化机制 垃圾回收机制把不需要的内存回收
优化jvm垃圾回收策略
优化catalina.sh配置文件。在catalina.sh配置文件中添加以下代码
```bash
# tomcat分配1G内存模板
JAVA_OPTS="-Djava.awt.headless=true -Dfile.encoding=UTF-8 -server -Xms1024m -Xmx1024m -XX:NewSize=512m -XX:MaxNewSize=512m"

JAVA_OPTS="-Djava.awt.headless=true -Dfile.encoding=UTF-8 -server -Xms800m -Xmx800m -XX:NewSize=400m -XX:MaxNewSize=400m"
# 重启服务
/application/tomcat/bin/shutdown.sh
/application/tomcat/bin/startup.sh
```
#### 3、下策：加足够大的内存
该项的资金投入较大
#### 4、下下策：每天0时定时重启tomcat
使用较为广泛

## 七、部署jenkins实战

下载地址：
```
<待补充>
```