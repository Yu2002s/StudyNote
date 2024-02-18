### 启动命令

##### 快速运行

```powershell
java -jar 路径
```

写入首字母后可以按 tab 键进行补全

##### 指定环境运行

```shell
java -jar 路径 --spring.profile.active=环境名
```

##### 其他一些指令

```bash
##### windows指令
netstat -ano # 所有的端口占用情况
netstat -ano | findstr "80" # 查询包含80字符的端口情况
tasklist | findstr "9480" # 查询指定pid的进程信息
tasklist # 查询所有进程
taskkill -f -pid 9480 # 杀死指定pid的进程
taskkill -f -t -im "进程名称" # 根据进程名称杀死进程
```

##### 环境定义方法

application.yml 文件中

```yaml
# 设置启用的环境
spring:
  profiles:
    active: test
server:
  servlet:
    context-path: /
---
# 开发环境
spring:
  profiles: dev
server:
  port: 80

---
# 生产环境
spring:
  profiles: pro
server:
  port: 81

---
# 测试环境
spring:
  profiles: test
server:
  port: 82
```

##### 修改端口

```powershell
java -jar 路径 --server.port=88
```

### Maven-依赖管理

#### 修改默认配置文件

1.进入到maven安装目录，找到conf文件夹，修改settings.xml配置文件

2.找到localrepository标签，去除注释，修改路径为仓库路径

##### 配置阿里云私服

添加settings.xml中的mirror标签

```xml
<mirror>  
    <id>alimaven</id>  
    <name>aliyun maven</name>  
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>          
</mirror>
```

  只可配置一个<mirror>(另一个要注释!) ，不然两个可能发生冲突，导致jar包无法下载

##### 配置环境变量

添加计算机的环境变量，在系统变量处添加变量MAVEN_HOME，设置值为maven的安装路径

在Path变量中添加值为%MAVEN_HOME%\bin

打开命令行输入命令 `mvn -v` ，出现版本号表示成功

#### POM配置文件

指的是项目对象模型，用来描述当前的maven项目

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- POM模型版本 -->
    <modelVersion>4.0.0</modelVersion>

    <!-- 当前项目坐标 -->
    <groupId>com.itheima</groupId>
    <artifactId>maven_project1</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <!-- 打包方式 -->
    <packaging>jar</packaging>
 
</project>
```

pom文件详解：

- <project> ：pom文件的根标签，表示当前maven项目
- <modelVersion> ：声明项目描述遵循哪一个POM模型版本
  - 虽然模型本身的版本很少改变，但它仍然是必不可少的。目前POM模型版本是4.0.0
- 坐标 ：<groupId>、<artifactId>、<version>
  - 定位项目在本地仓库中的位置，由以上三个标签组成一个坐标
- <packaging> ：maven项目的打包方式，通常设置为jar或war（默认值：jar）

##### 坐标

Maven坐标主要组成

* groupId：定义当前Maven项目隶属组织名称（通常是域名反写，例如：com.itheima）
* artifactId：定义当前Maven项目名称（通常是模块名称，例如 order-service、goods-service）
* version：定义当前项目版本号

##### 排除依赖

```xml
<dependencies>
        <dependency>
            <groupId>xyz.jdynb</groupId>
            <artifactId>maven-project01</artifactId>
            <version>1.0-SNAPSHOT</version>
            <exclusions>
                <exclusion>
                    <groupId>ch.qos.logback</groupId>
                    <artifactId>logback-classic</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
```

设置 exclusion 可以排除不需要的依赖

##### 依赖范围

**scope**

compile: 默认，主程序、测试、打包

test: 测试环境

provided：主程序、测试

runtime：测试、打包

```xml
  <groupId>xyz.jdynb</groupId>
  <artifactId>maven-project01</artifactId>
  <version>1.0-SNAPSHOT</version>
  <scope>test</scope>
```

### HTTP协议

#### 请求协议

在HTTP1.1版本中，浏览器访问服务器的几种方式： 

| 请求方式 | 请求说明                                                     |
| :------: | :----------------------------------------------------------- |
| **GET**  | 获取资源。<br/>向特定的资源发出请求。例：http://www.baidu.com/s?wd=itheima |
| **POST** | 传输实体主体。<br/>向指定资源提交数据进行处理请求（例：上传文件），数据被包含在请求体中。 |
| OPTIONS  | 返回服务器针对特定资源所支持的HTTP请求方式。<br/>因为并不是所有的服务器都支持规定的方法，为了安全有些服务器可能会禁止掉一些方法，例如：DELETE、PUT等。那么OPTIONS就是用来询问服务器支持的方法。 |
|   HEAD   | 获得报文首部。<br/>HEAD方法类似GET方法，但是不同的是HEAD方法不要求返回数据。通常用于确认URI的有效性及资源更新时间等。 |
|   PUT    | 传输文件。<br/>PUT方法用来传输文件。类似FTP协议，文件内容包含在请求报文的实体中，然后请求保存到URL指定的服务器位置。 |
|  DELETE  | 删除文件。<br/>请求服务器删除Request-URI所标识的资源         |
|  TRACE   | 追踪路径。<br/>回显服务器收到的请求，主要用于测试或诊断      |
| CONNECT  | 要求用隧道协议连接代理。<br/>HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器 |

Host: 表示请求的主机名

User-Agent: 浏览器版本。 例如：Chrome浏览器的标识类似Mozilla/5.0 ...Chrome/79 ，IE浏览器的标识类似Mozilla/5.0 (Windows NT ...)like Gecko

Accept：表示浏览器能接收的资源类型，如text/*，image/*或者*/*表示所有；

Accept-Language：表示浏览器偏好的语言，服务器可以据此返回不同语言的网页；

Accept-Encoding：表示浏览器可以支持的压缩类型，例如gzip, deflate等。

Content-Type：请求主体的数据类型

Content-Length：数据主体的大小（单位：字节）

Cache-Control：指示客户端应如何缓存，例如max-age=300表示可以最多缓存300秒 ;

#### 响应码

| 状态码分类 | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 1xx        | **响应中** --- 临时状态码。表示请求已经接受，告诉客户端应该继续请求或者如果已经完成则忽略 |
| 2xx        | **成功** --- 表示请求已经被成功接收，处理已完成              |
| 3xx        | **重定向** --- 重定向到其它地方，让客户端再发起一个请求以完成整个处理 |
| 4xx        | **客户端错误** --- 处理发生错误，责任在客户端，如：客户端的请求一个不存在的资源，客户端未被授权，禁止访问等 |
| 5xx        | **服务器端错误** --- 处理发生错误，责任在服务端，如：服务端抛出异常，路由出错，HTTP版本不支持等 |

### MyBatis

### Tomcat

官方网站：https://tomcat.apache.org/

#### 启动

进入bin目录

1.双击startup.bat(windows)

2.安装服务

```bash
service.bat install
```

双击tomcat.ext运行

#### 配置

解决控制台中文乱码问题:修改conf/logging.properties

```properties
java.util.logging.ConsoleHandler.level = FINE
java.util.logging.ConsoleHandler.encodeing = GBK # 将UTF-8改为GBK
```

修改tomcat端口：/conf/server.xml

```xml
<Connector port="xxx" .../>
```

### SpringBoot

#### @RestController

请求处理类，@Contoller与@ResponseBody结合

#### @ResponseBody

响应json数据

#### @RequestBody

接收json数据(请求体数据)

#### @RequestMapping

#### @GetMapping

#### @PostMapping

##### 接收普通参数

```java
// 请求地址
@RequestMapping("/simpleParam")
public String simpleParam(@RequestParam(name = "name",/*参数名*/ required = false/*非必须传递*/) String username, Integer age) {
    System.out.println(username);
    System.out.println(age);
    return "ok";
}
```

##### 接收pojo实体类

User.java

```java
public class User {

    String name;
    Integer age;

    Address address;
    ...
}
```

Address.java

```java
public class Address {

    String province;

    String city;
    ...
}
```

```java
@RequestMapping("/simpleParam")
public String simplePojo(User user) {
    System.out.println(user.getName());
    System.out.println(user.getAge());
    return "ok";
}
```

##### 接收多个相同属性

使用数组接收

```java
// 请求地址：http://localhost:8080/arrayParam?hobby=game&hobby=学习
@RequestMapping("/arrayParam")
public String arrayParam(String[] hobby) {
    System.out.println(Arrays.toString(hobby));
    return "ok";
}
```

使用集合接收

```java
// 需要加上@RequestParam才可以正常接收到
@RequestMapping("/listParam")
public String listParam(@RequestParam List<String> hobby) {
    System.out.println(hobby);
    return "ok";
}
```

##### 接收时间类型

```java
@RequestMapping("/dateParam")
public String dateParam(@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDateTime updateTime) {
    System.out.println(updateTime);
    return "ok";
}
```

##### 接收json数据

```java
// 注意添加@RequestBody注解，解析body参数
@RequestMapping("/jsonParam")
public String jsonParam(@RequestBody User user) {
    System.out.println(user);
    return "ok";
}
```

##### 接收路径参数

使用@PathVariable注解接收参数

```java
@RequestMapping("/path/{id}")
public String pathParam(@PathVariable("id") Integer id) {
    System.out.println(id);
    return "ok";
}
```

#### 统一返回数据

```java
public class Result {

    private Integer code;
    private String msg;
    private Object data;

    public Result(Integer code, String msg) {
        this.code = code;
        this.msg = msg;
    }

    public Result(Integer code, String msg, Object data) {
        this.code = code;
        this.msg = msg;
        this.data = data;
    }

    public static Result success() {
        return new Result(1, null);
    }
    
    public static Result success(Object data) {
        return new Result(1, null, data);
    }
    
    public static Result success(Object data, String msg) {
        return new Result(1, msg, data);
    }
    
    public static Result error(String msg) {
        return new Result(0, msg);
    } 

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }

    @Override
    public String toString() {
        return "Result{" +
                "code=" + code +
                ", msg='" + msg + '\'' +
                ", data=" + data +
                '}';
    }
}

```

#### 解析XML

导入坐标

```xml
<dependency>
    <groupId>org.dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>2.1.3</version>
</dependency>
```

设置解析工具类

```java
public class XmlParserUtils {

    public static <T> List<T> parse(String file , Class<T> targetClass)  {
        ArrayList<T> list = new ArrayList<T>(); //封装解析出来的数据
        try {
            //1.获取一个解析器对象
            SAXReader saxReader = new SAXReader();
            //2.利用解析器把xml文件加载到内存中,并返回一个文档对象
            Document document = saxReader.read(new File(file));
            //3.获取到根标签
            Element rootElement = document.getRootElement();
            //4.通过根标签来获取 user 标签
            List<Element> elements = rootElement.elements("emp");

            //5.遍历集合,得到每一个 user 标签
            for (Element element : elements) {
                //获取 name 属性
                String name = element.element("name").getText();
                //获取 age 属性
                String age = element.element("age").getText();
                //获取 image 属性
                String image = element.element("image").getText();
                //获取 gender 属性
                String gender = element.element("gender").getText();
                //获取 job 属性
                String job = element.element("job").getText();

                //组装数据
                Constructor<T> constructor = targetClass.getDeclaredConstructor(String.class, Integer.class, String.class, String.class, String.class);
                constructor.setAccessible(true);
                T object = constructor.newInstance(name, Integer.parseInt(age), image, gender, job);

                list.add(object);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return list;
    }

}
```

在controller中解析数据

```java
@RequestMapping("/listEmp")
public Result list() {
    // 加载并解析xml文件
    String file = this.getClass().getClassLoader().getResource("emp.xml").getFile();
    System.out.println(file);
    List<Emp> empList = XmlParserUtils.parse(file, Emp.class);
    empList.forEach(emp -> {
        if ("1".equals(emp.getGender())) {
            emp.setGender("男");
        } else if ("2".equals(emp.getGender())) {
            emp.setGender("女");
        }
    });
    return Result.success(empList);
}
```

### Bean声明

#### @Controller 

@Component的衍生注解，标注在控制器类上

#### @Service

@Component的衍生注解，标注在业务类上

#### @Repository

@Component的衍生注解，标注在数据访问类上

#### @Component

声明bean的注解，不属于以上三类时，用此注解

#### @ComponentScan

组件扫描，配置在启动类上，设置包扫描的路径，默认扫描启动类所在包以及子包

```java
@ComponentScan({"xyz.jdynb", "dao"}) // basePackages
```

#### @Autowired

自动装配，运行时IOC容器会通过该类型的bean对象

#### @Primary

设置bean的优先级，如果存在两个以及以上相同类型的bean时，优先使用此注解的bean

#### @Qualifier

指定注入对应名字的bean，配合@Autowired使用

```java
@Autowired
@Qualifier("empDao2")
private EmpDao empDao;
```

#### @Resource

默认按照名字注入，没有名字按照类型

```java
@Resource(name = "empDao2")
private EmpDao empDao;
```

