### 自动装配原理
#### pom
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

#### 主程序  

点击出程序注解@SpringBootApplication
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
会看到核心是@SpringBootConfiguration和@EnableAutoConfiguration

- @SpringBootConfiguration 就是springboot的配置，点进去会看到
``` java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Configuration
public @interface SpringBootConfiguration {
```
@Configuration说明他是个spring配置类。点进去。
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
```
@Component说明也是一个spring组件。

- @EnableAutoConfiguration 自动配置，点进去
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```
@AutoConfigurationPackage自动配置包
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```
@Import(AutoConfigurationPackages.Registrar.class)导入一个选择器。看Registrar可以看到
```java
static class Registrar implements ImportBeanDefinitionRegistrar, DeterminableImports {

	@Override
	public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
		register(registry, new PackageImports(metadata).getPackageNames().toArray(new String[0]));
	}

	@Override
	public Set<Object> determineImports(AnnotationMetadata metadata) {
		return Collections.singleton(new PackageImports(metadata));
	}

}
```
要注册这些元数据metadata，PackageImports导入这些包。暂时放在这里。
回看@EnableAutoConfiguration还有个@Import(AutoConfigurationImportSelector.class)选择器。点击AutoConfigurationImportSelector类  
```java
public class AutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware,
		ResourceLoaderAware, BeanFactoryAware, EnvironmentAware, Ordered {

...

private ResourceLoader resourceLoader;

private ConfigurationClassFilter configurationClassFilter;

@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
	if (!isEnabled(annotationMetadata)) {
		return NO_IMPORTS;
	}
	AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(annotationMetadata);
	return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```
有个selectImports选择组件。会选择项目中pom.xml配置的比如spring-boot-starter-web这些东西。

```java
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
```
List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);获取所有的配置。
获取候选配置函数
```java
protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
	List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
			getBeanClassLoader());
	Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
			+ "are using a custom packaging, make sure that file is correct.");
	return configurations;
}
```

---

关系是：
>@SpringBootConfiguration：
>>@Configuration
>>@Component
>@EnableAutoConfiguration:  
>>@AutoConfigurationPackage:  
>>>>@Import(AutoConfigurationPackages.Registrar.class) 自动配置`包注册`。这与规定包要在springboot启动类下面文件夹有关系。  
>>>>@Import(AutoConfigurationImportSelector.class) 自动配置导入选择。  

---

[springboot-starter]:/image/springboot-starter.jpg "springboot starter"
