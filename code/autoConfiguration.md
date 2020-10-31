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
- spring-boot-dependencies：核心依赖。在父工程中。
