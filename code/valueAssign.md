在pojo文件夹创建Dog类
```java
@Component
public class Dog {

    private String name;
    private Integer age;
...
constructor...
setter and getter ...
tostring
```
创建Person类
```java
@Component
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
constructor ...
setter and getter ...
tostring
```
有种赋值方式
```java
@Component
public class Dog {

    @Value("WangCai")
    private String name;
    @Value("3")
    private Integer age;
    ...
```
在测试类测试以下，springboot/code/helloword/src/test/java/com/lanjunc/helloword/HellowordApplicationTests.java
```java
@SpringBootTest
class HellowordApplicationTests {

    @Autowired
    private Dog dog;

    @Test
    void contextLoads() {
        System.out.println(dog);
    }

}
```
如果有多个狗可以用@Qualifier制定具体哪条狗。运行以下。
```log
Dog{name='WangCai', age=3}
```
现在在里面赋值很麻烦，能不能从外面赋值。这时候yaml的优势就体现出来了。  
在resources下创建个application.yaml文件。拿赋值Person为例，
```java
@Component
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
```
```yaml
person:
  name: qinjing
  age: 3
  happy: false
  birth: 2019/11/02
  maps: {k1: v1, k2: v2}
  lists:
    - code
    - music
    - girl
  dog:
    name: WangCai
    age: 3
```
如果能把yaml的值直接赋值给Person，那就有值了。以前是可以new出来赋值，spring可以用@Value，而yaml可以直接把值注入进去。  
加个注解@ConfigurationProperties。IDEA会报红，点击Open Document浏览器打开https://docs.spring.io/spring-boot/docs/2.1.9.RELEASE/reference/html/configuration-metadata.html#configuration-metadata-annotation-processor  
让你添加一个依赖，报红没了。
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-configuration-processor</artifactId>
	<optional>true</optional>
</dependency>
```
加@ConfigurationProperties(prefix = "person")
```java
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
```
person就是前缀，就会去yaml的person中找下面的属性。  
@ConfigurationProperties作用，将配置文件中配置的每一个属性值，映射到这个组建中。告诉springboot将本类中所有属性和配置文件中相关的配置进行绑定。  
参数prefix = "person"：将配置文件中person下面的属性一一对应。
```java
@SpringBootTest
class HellowordApplicationTests {

    @Autowired
    private Person person;

    @Test
    void contextLoads() {
        System.out.println(person);
    }

}
```
测试用例运行。
```log
Person{name='qinjing', age=3, happy=false, birth=Sat Nov 02 00:00:00 CST 2019, maps={k1=v1, k2=v2}, lists=[code, music, girl], dog=Dog{name='WangCai', age=3}}
```
配置文件除了yaml还有我们之前经常用的properties
```java
@Component
//@ConfigurationProperties(prefix = "person")
@PropertySource(value = "classpath:lanjunc.properties") // 加载制定的配置文件
public class Person {
    @Value("${name}")
    private String name;
    private Integer age;
    private Boolean happy;
    private Date birth;
    private Map<String, Object> maps;
    private List<Object> lists;
    private Dog dog;
```
```properties
name=lanjunc
```
运行测试用例。
```log
Person{name='lanjunc', age=null, happy=null, birth=null, maps=null, lists=null, dog=null}
```
name赋值上了。  
yaml还有一些特殊语法。
```yaml
person:
  name: lanjunc${random.uuid}
  age: ${random.int}
  happy: false
  birth: 2019/11/02
  maps: {k1: v1, k2: v2}
  lists:
    - code
    - music
    - girl
  dog:
    name: ${person.hello:hello}_WangCai
    age: 3
```
random.uuid生成uuid还有random.int生成随机数，${person.hello:hello}person中没有hello属性，所有走默认值，冒号后面的`hello`
```log
Person{name='lanjuncf4fb2231-0b33-4f9d-9db1-0b4d455f277a', age=1098036223, happy=false, birth=Sat Nov 02 00:00:00 CST 2019, maps={k1=v1, k2=v2}, lists=[code, music, girl], dog=Dog{name='hello_WangCai', age=3}}
```
如果yaml中加上hello的属性，会输出对应的值。这样赋值属性叫占位符。
```yaml
person:
  name: lanjunc${random.uuid}
  age: ${random.int}
  happy: false
  birth: 2019/11/02
  maps: {k1: v1, k2: v2}
  hello: lalala
  lists:
    - code
    - music
    - girl
  dog:
    name: ${person.hello:hello}_WangCai
    age: 3
```
```log
Person{name='lanjunc4b8e5a36-18a2-43ec-8829-b4152db19656', age=741211421, happy=false, birth=Sat Nov 02 00:00:00 CST 2019, maps={k1=v1, k2=v2}, lists=[code, music, girl], dog=Dog{name='lalala_WangCai', age=3}}
```
配置文件中能写这样的判断，代码中可以少写很多。

### 多环境配置文件位置

1.file:./config/ （在springboot项目目录下创建config目录，里面创建application.yaml配置文件） `优先级最高`  
2.file:.  （在springboot项目目录下创建application.yaml配置文件） `优先级第二`  
3.classpath:/config/ （在项目的resources下创建config目录，里面创建application.yaml配置文件） `优先级第三`  
4.classpath:/ （在项目的resources下创建application.yaml配置文件） `优先级第四`  
当项目中有开发环境配置application-dev.properties，还有测试环境配置application-test.properties，默认的application.properties。  
在application.properties
```properties
# spring的多环境配置：可以选择激活哪一个配置文件
spring.profiles.active=test
```
则运行走的就是application-test.properties配置文件。  

还是比较麻烦的，用yaml  
用`---` 分模块
```yaml
server:
  port: 8081
spring:
  profiles:
    active: dev # 配置dev就会走下面dev的模块设置，port为8082，如果这里不写，则走默认的8081。
  
---

server:
  port: 8082
spring:
  profiles: dev
---
server:
  port: 8083
spring:
  profiles: test
```
yaml一个文件可以当多个properties文件用。用`---` 横杠分割。  

### 自动配置再理解
```java
@SpringBootApplication
public class HelloWorldApplication {

    public static void main(String[] args) {
        SpringApplication.run(HelloWorldApplication.class, args);
    }

}
```
点击@SpringBootApplication
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
都会有@EnableAutoConfiguration，自动导入配置
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```
都会有AutoConfigurationImportSelector.class选择导入方案。
```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {
		...
	// 获取实体
	protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
		if (!isEnabled(annotationMetadata)) {
			return EMPTY_ENTRY;
		}
		AnnotationAttributes attributes = getAttributes(annotationMetadata);
		List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
		configurations = removeDuplicates(configurations);
		Set<String> exclusions = getExclusions(annotationMetadata, attributes);
		checkExcludedClasses(configurations, exclusions);
		configurations.removeAll(exclusions);
		configurations = getConfigurationClassFilter().filter(configurations);
		fireAutoConfigurationImportEvents(configurations, exclusions);
		return new AutoConfigurationEntry(configurations, exclusions);
	}
	// 获取候选配置 
	protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}
	// 找到
	protected Class<?> getSpringFactoriesLoaderFactoryClass() {
		return EnableAutoConfiguration.class;
	}
	// 以上函数获取标注了EnableAutoConfiguration的所有类，然后用getCandidateConfigurations函数中loadFactoryNames加载一些配置。
	public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
		String factoryTypeName = factoryType.getName();
		return loadSpringFactories(classLoader).getOrDefault(factoryTypeName, Collections.emptyList());
	}
```
https://www.bilibili.com/video/BV1PE411i7CV?p=12 1:30.继续学。
