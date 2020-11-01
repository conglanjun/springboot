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

