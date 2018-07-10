# springboot series

## 参考来源

- [[纯洁的微笑](http://www.ityouknow.com/)](http://www.ityouknow.com/springboot/2017/05/09/springboot-deploy.html)

## 开发

### 创建springboot项目

创建一个简单的springboot项目,使用管方在线工具[Spring Initializr](http://start.spring.io/)或使用eclipse中的`spring tool suite(STS)`插件。

### pom.xml基础配置

```xml
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>1.5.9.RELEASE</version>
  <relativePath />
</parent>

<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
  </dependency>
  
  <!-- 热启动插件 -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
  </dependency>
  
  <!-- 性能测试 -->
  <dependency>
    <groupId>org.databene</groupId>
    <artifactId>contiperf</artifactId>
    <version>2.1.0</version>
    <scope>test</scope>
  </dependency>
  
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</dependencies>
```

### 自定义banner

在`src\main\resources`目录下创建一个`banner.txt`文件，启动项目时就会显示这文件中的内容.

[这里](http://patorjk.com/software/taag)可以生成ascii文字，复制方到`banner.txt`文件中。

### 静态文件设置

在 `src\main\resources` 目录下的`application.yml`文件中添加

```yml
spring:
  resources:
    static-locations: classpath:/static/,classpath:/views/
```

**备注：** static下放index.html，访问`http://localhost:8080/ `会自动找它，找不到就去views看有没有index.html。

## 测试

### 单元测试

1. 在pom中添加包依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-test</artifactId>
	<scope>test</scope>
</dependency>
```

2. 开发测试类

以最简单的helloworld为例，在测试类的类头部需要添加：`@RunWith(SpringRunner.class)`和`@SpringBootTest`注解，在测试方法的顶端添加`@Test`即可，最后在方法上点击右键run就可以运行。

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {

	@Test
	public void hello() {
		System.out.println("hello world");
	}

}
```

实际使用中，可以按照项目的正常使用去注入dao层代码或者是service层代码进行测试验证，spring-boot-starter-test提供很多基础用法，更难得的是增加了对Controller层测试的支持。

### mock工具

[jmockdata](https://github.com/jsonzou/jmockdata)

## 部署

###  jar 包形式部署

如果你使用的是maven来管理项目，执行以下命令既可以

```sh
## cd 项目跟目录（和pom.xml同级）
mvn clean package
## 或者执行下面的命令
## 排除测试代码后进行打包
mvn clean package  -Dmaven.test.skip=true
```

打包完成后jar包会生成到target目录下，命名一般是 项目名+版本号.jar

**启动jar包命令**

```sh
java -jar  target/spring-boot-scheduler-1.0.0.jar
```

这种方式，只要控制台关闭，服务就不能访问了。下面我们使用在后台运行的方式来启动:

```sh
nohup java -jar target/spring-boot-scheduler-1.0.0.jar &
```

也可以在启动的时候选择读取不同的配置文件

```sh
java -jar app.jar --spring.profiles.active=dev
```

也可以在启动的时候设置jvm参数

```sh
java -Xms10m -Xmx80m -jar app.jar &
```

**gradle**
如果使用的是gradle,使用下面命令打包

```sh
gradle build
java -jar build/libs/mymodule-0.0.1-SNAPSHOT.jar
```



### war 包形式部署

打成war包一般可以分两种方式来实现，第一种可以通过eclipse这种开发工具来导出war包，另外一种是使用命令来完成，这里主要介绍后一种

**1、maven项目，修改pom包**

将
```xml
<packaging>jar</packaging>  
```
改为
```xml
<packaging>war</packaging>
```

**2、打包时排除tomcat.**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-tomcat</artifactId>
	<scope>provided</scope>
</dependency>
```

在这里将scope属性设置为provided，这样在最终形成的WAR中不会包含这个JAR包，因为Tomcat或Jetty等服务器在运行时将会提供相关的API类。

**3、注册启动类**

创建ServletInitializer.java，继承SpringBootServletInitializer ，覆盖configure()，把启动类Application注册进去。外部web应用服务器构建Web Application Context的时候，会把启动类添加进去。

```java
public class ServletInitializer extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(Application.class);
    }
}
```



最后执行

```sh
mvn clean package  -Dmaven.test.skip=true
```

会在target目录下生成：项目名+版本号.war文件，拷贝到tomcat服务器中启动即可。

**gradle**

如果使用的是gradle,基本步奏一样，build.gradle中添加war的支持，排除spring-boot-starter-tomcat：

```sh
...

apply plugin: 'war'

...

dependencies {
    compile("org.springframework.boot:spring-boot-starter-web:1.4.2.RELEASE"){
    	exclude mymodule:"spring-boot-starter-tomcat"
    }
}
...
```
再使用构建命令

gradle build
war会生成在build\libs 目录下。



## 生产运维

### 查看JVM参数的值

可以根据java自带的jinfo命令：

```
jinfo -flags pid

```

来查看jar 启动后使用的是什么gc、新生代、老年代分批的内存都是多少，示例如下：

```sh
-XX:CICompilerCount=3 -XX:InitialHeapSize=234881024 -XX:MaxHeapSize=3743416320 -XX:MaxNewSize=1247805440 -XX:MinHeapDeltaBytes=524288 -XX:NewSize=78118912 -XX:OldSize=156762112 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseFastUnorderedTimeStamps -XX:+UseParallelGC
```

- `-XX:CICompilerCount` ：最大的并行编译数
- `-XX:InitialHeapSize` 和 `-XX:MaxHeapSize` ：指定JVM的初始和最大堆内存大小
- `-XX:MaxNewSize` ： JVM堆区域新生代内存的最大可分配大小
- `-XX:+UseParallelGC` ：垃圾回收使用Parallel收集器

### 如何重启

**简单粗暴**

直接kill掉进程再次启动jar包

```
ps -ef|grep java 
##拿到对于Java程序的pid
kill -9 pid
## 再次重启
Java -jar  xxxx.jar

```

当然这种方式比较传统和暴力，所以建议大家使用下面的方式来管理

**脚本执行**

如果使用的是maven,需要包含以下的配置

```
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
        <executable>true</executable>
    </configuration>
</plugin>

```

如果使用是gradle，需要包含下面配置

```
springBoot {
    executable = true
}

```

启动方式：

1、 可以直接`./yourapp.jar` 来启动

2、注册为服务

也可以做一个软链接指向你的jar包并加入到`init.d`中，然后用命令来启动。

init.d 例子:

```sh
ln -s /var/yourapp/yourapp.jar /etc/init.d/yourapp
chmod +x /etc/init.d/yourapp
```

这样就可以使用`stop`或者是`restart`命令去管理你的应用。

```sh
/etc/init.d/yourapp start|stop|restart
```

或者

```sh
service yourapp start|stop|restart
```

到此 springboot项目如何测试、联调和打包投产均已经介绍完，以后可以找时间研究一下springboot的自动化运维，以及spring boot 和docker相结合的使用。