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
