## 第一个springboot程序
### 环境
- jdk 11
- maven 3.6.3
- springboot：新版
- IDEA
### 官方：提供了一个快速生成的网站！IDEA集成了这个网站
- 当前springboot的最新版：![][springboot-version]
- spring网站start创建项目 https://start.spring.io/
![][springboot-initializr]
- 选择依赖是web：  
![][springboot-initializr-dependencies]

### 用IDEA创建
- 下载idea
- 下载jdk 11  
```shell
sudo vim /etc/profile  
export M2_HOME=/home/klfy/software/apache-maven-3.6.3  
export JAVA_HOME=/home/klfy/software/jdk-11.0.9  
export CLASSPATH=.:${JAVA_HOME}/lib  
export PATH=$M2_HOME/bin:${JAVA_HOME}/bin:$PATH  
```
- 下载maven 3.6.3  
同上
```shell
source /etc/profile
```

### springboot项目代码
```java
@SpringBootApplication
public class HellowordApplication {

    public static void main(String[] args) {
        SpringApplication.run(HellowordApplication.class, args);
    }

}
```  
在启动类同级目录下创建controller,dao,pojo,service。会扫描这些目录的文件。  
![][springboot-project-directory]

创建controller详见代码。看具体springboot自动启动内容去子项目查看。  
[helloword代码](https://github.com/conglanjun/springboot/tree/main/code/helloword "helloword")

### [springboot自动装配原理](https://github.com/conglanjun/springboot/tree/main/code/autoConfiguration.md)

### [springboot自动启动原理](https://github.com/conglanjun/springboot/tree/main/code/autoRun.md)

### [springboot给属性赋值几种方式](https://github.com/conglanjun/springboot/tree/main/code/valueAssign.md)














---

[springboot-version]:/image/springboot_version.jpg "springboot版本号"
[springboot-initializr]:/image/springboot-initializr.jpg "springboot initializr"
[springboot-initializr-dependencies]:/image/springboot-initializr-dependencies.jpg "springboot initializr dependencies"
[springboot-project-directory]:/image/springboot-project-directory.jpg "springboot project"
