### 自动装配原理
点击pom.xml中的spring-boot-starter-parent进入spring-boot-starter-parent-2.3.5.RELEASE.pom
```xml
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.5.RELEASE</version>
  </parent>
```
里面还有个parent。点进入进入spring-boot-dependencies-2.3.5.RELEASE.pom管理了大量的jar包版本。
- spring-boot-dependencies：核心依赖。在父工程中。版本都是在这里管理的。  
资源过滤配置好了。
```xml
<resources>
  <resource>
    <directory>${basedir}/src/main/resources</directory>
    <filtering>true</filtering>
    <includes>
      <include>**/application*.yml</include>
      <include>**/application*.yaml</include>
      <include>**/application*.properties</include>
    </includes>
  </resource>
  <resource>
    <directory>${basedir}/src/main/resources</directory>
    <excludes>
      <exclude>**/application*.yml</exclude>
      <exclude>**/application*.yaml</exclude>
      <exclude>**/application*.properties</exclude>
    </excludes>
  </resource>
</resources>
```
- 我们在写或者引入springboot依赖的时候不需要写版本，就因为有版本仓库。

#### 启动器
- 
```shell
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```
- springboot启动器。就是springboot的启动场景。
- 比如spring-boot-starter-web，他会帮我们自动导入web环境所有的依赖。
- spring-boot会把所有的功能场景都变成一个个的启动器。
- 再看一下spring-boot-dependencies-xxx.pom文件
```xml
<properties>
    <activemq.version>5.15.13</activemq.version>
    <antlr2.version>2.7.7</antlr2.version>
    <appengine-sdk.version>1.9.82</appengine-sdk.version>
    <artemis.version>2.12.0</artemis.version>
    <aspectj.version>1.9.6</aspectj.version>
    <assertj.version>3.16.1</assertj.version>
    <atomikos.version>4.0.6</atomikos.version>
    <awaitility.version>4.0.3</awaitility.version>
    <bitronix.version>2.1.4</bitronix.version>
  ...
```
里面有很多版本依赖。  

- 我们要使用什么功能，就找到对应的启动器就行了。`starter`。
可以去官网看Dependency Versions会有很多依赖jar包。还可以找到`starter`
![][springboot-starter]
---

[springboot-starter]:/image/springboot-starter.jpg "springboot starter"
