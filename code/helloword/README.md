```java
@SpringBootApplication
public class HellowordApplication {

    public static void main(String[] args) {
        SpringApplication.run(HellowordApplication.class, args);
    }

}
```
进入@SpringBootApplication
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```
进入@SpringBootConfiguration
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
```
进入@Configuration
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
```
看到就是@Component本身就是spring组件。  
查看pom.xml
```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>2.3.5.RELEASE</version>
	<relativePath/> <!-- lookup parent from repository -->
</parent>
```
有一个父项目。  
看下依赖
```xml
<dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
web依赖，tomcat，dispatcherServlet，xml...如果去掉这个依赖包。项目中@SpringBootApplication报红。
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
    <exclusions>
	<exclusion>
	    <groupId>org.junit.vintage</groupId>
	    <artifactId>junit-vintage-engine</artifactId>
	</exclusion>
    </exclusions>
</dependency>
```
单元测试。  
springboot所有的依赖都是以spring-boot-starter-开头的。  
运行maven插件的package。会在target生成jar包。直接启动就就可以启动springboot项目。
```shell
java -jar ./helloword-0.0.1-SNAPSHOT.jar
```
![][springboot-start.jpg]

--------------------------------
[springboot-start]:/image/springboot-start.jpg "springboot 启动"
