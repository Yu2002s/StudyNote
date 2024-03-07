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

#### 配置文件

##### application.yml

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver # 驱动
    url: jdbc:mysql://localhost:3306/db01?serverTimezone=UTC
    username: root
    password: jdy200255
mybatis:
  mapper-locations: classpath:mapper/*xml
  type-aliases-package: com.example.mybatis.pojo
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
    # 驼峰命名自动映射
    map-underscore-to-camel-case: true
logging:
  level:
    root: info
  config: classpath:logback.xml
  file:
    name: server.log
mybatis-plus: # mybatis-plus配置信息
  type-aliases-package: com.example.domain # 包别名
  global-config: # 全局配置
    db-config: # 数据库配置
      id-type: auto # id类型
  configuration: 
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl #日志实现
```

##### mybatis-config.xml

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 别名简化 -->
    <typeAliases>
        <package name="com.itheima.pojo"/>
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!-- 连接信息 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="2002"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- 加载sql映射文件 -->
<!--        <mapper resource="com/itheima/mapper/UserMapper.xml"/>-->

        <!-- Mapper代理方式 使用此方法映射，无需手动引入映射文件 -->
        <package name="com.itheima.mapper"/>
    </mappers>
</configuration>
```

###### 属性

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。	

```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

设置好的属性可以在整个配置文件中用来替换需要动态配置的属性值。

```xml
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

这个例子中的 username 和 password 将会由 properties 元素中设置的相应值来替换。 driver 和 url 属性将会由 config.properties 文件中对应的值来替换。这样就为配置提供了诸多灵活选择。

###### 设置

这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。 下表描述了设置中各项设置的含义、默认值等。

| 设置名                           | 描述                                                         | 有效值                                                       | 默认值                                                |
| :------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :---------------------------------------------------- |
| cacheEnabled                     | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true                                                  |
| lazyLoadingEnabled               | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false                                                | false                                                 |
| aggressiveLazyLoading            | 开启时，任一方法的调用都会加载该对象的所有延迟加载属性。 否则，每个延迟加载属性会按需加载（参考 `lazyLoadTriggerMethods`)。 | true \| false                                                | false （在 3.4.1 及之前的版本中默认为 true）          |
| multipleResultSetsEnabled        | 是否允许单个语句返回多结果集（需要数据库驱动支持）。         | true \| false                                                | true                                                  |
| useColumnLabel                   | 使用列标签代替列名。实际表现依赖于数据库驱动，具体可参考数据库驱动的相关文档，或通过对比测试来观察。 | true \| false                                                | true                                                  |
| useGeneratedKeys                 | 允许 JDBC 支持自动生成主键，需要数据库驱动支持。如果设置为 true，将强制使用自动生成主键。尽管一些数据库驱动不支持此特性，但仍可正常工作（如 Derby）。 | true \| false                                                | False                                                 |
| autoMappingBehavior              | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示关闭自动映射；PARTIAL 只会自动映射没有定义嵌套结果映射的字段。 FULL 会自动映射任何复杂的结果集（无论是否嵌套）。 | NONE, PARTIAL, FULL                                          | PARTIAL                                               |
| autoMappingUnknownColumnBehavior | 指定发现自动映射目标未知列（或未知属性类型）的行为。`NONE`: 不做任何反应`WARNING`: 输出警告日志（`'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'` 的日志等级必须设置为 `WARN`）`FAILING`: 映射失败 (抛出 `SqlSessionException`) | NONE, WARNING, FAILING                                       | NONE                                                  |
| defaultExecutorType              | 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（PreparedStatement）； BATCH 执行器不仅重用语句还会执行批量更新。 | SIMPLE REUSE BATCH                                           | SIMPLE                                                |
| defaultStatementTimeout          | 设置超时时间，它决定数据库驱动等待数据库响应的秒数。         | 任意正整数                                                   | 未设置 (null)                                         |
| defaultFetchSize                 | 为驱动的结果集获取数量（fetchSize）设置一个建议值。此参数只可以在查询设置中被覆盖。 | 任意正整数                                                   | 未设置 (null)                                         |
| defaultResultSetType             | 指定语句默认的滚动策略。（新增于 3.5.2）                     | FORWARD_ONLY \| SCROLL_SENSITIVE \| SCROLL_INSENSITIVE \| DEFAULT（等同于未设置） | 未设置 (null)                                         |
| safeRowBoundsEnabled             | 是否允许在嵌套语句中使用分页（RowBounds）。如果允许使用则设置为 false。 | true \| false                                                | False                                                 |
| safeResultHandlerEnabled         | 是否允许在嵌套语句中使用结果处理器（ResultHandler）。如果允许使用则设置为 false。 | true \| false                                                | True                                                  |
| mapUnderscoreToCamelCase         | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 | true \| false                                                | False                                                 |
| localCacheScope                  | MyBatis 利用本地缓存机制（Local Cache）防止循环引用和加速重复的嵌套查询。 默认值为 SESSION，会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地缓存将仅用于执行语句，对相同 SqlSession 的不同查询将不会进行缓存。 | SESSION \| STATEMENT                                         | SESSION                                               |
| jdbcTypeForNull                  | 当没有为参数指定特定的 JDBC 类型时，空值的默认 JDBC 类型。 某些数据库驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 | JdbcType 常量，常用值：NULL、VARCHAR 或 OTHER。              | OTHER                                                 |
| lazyLoadTriggerMethods           | 指定对象的哪些方法触发一次延迟加载。                         | 用逗号分隔的方法列表。                                       | equals,clone,hashCode,toString                        |
| defaultScriptingLanguage         | 指定动态 SQL 生成使用的默认脚本语言。                        | 一个类型别名或全限定类名。                                   | org.apache.ibatis.scripting.xmltags.XMLLanguageDriver |
| defaultEnumTypeHandler           | 指定 Enum 使用的默认 `TypeHandler` 。（新增于 3.4.5）        | 一个类型别名或全限定类名。                                   | org.apache.ibatis.type.EnumTypeHandler                |
| callSettersOnNulls               | 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这在依赖于 Map.keySet() 或 null 值进行初始化时比较有用。注意基本类型（int、boolean 等）是不能设置成 null 的。 | true \| false                                                | false                                                 |
| returnInstanceForEmptyRow        | 当返回行的所有列都是空时，MyBatis默认返回 `null`。 当开启这个设置时，MyBatis会返回一个空实例。 请注意，它也适用于嵌套的结果集（如集合或关联）。（新增于 3.4.2） | true \| false                                                | false                                                 |
| logPrefix                        | 指定 MyBatis 增加到日志名称的前缀。                          | 任何字符串                                                   | 未设置                                                |
| logImpl                          | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置                                                |
| proxyFactory                     | 指定 Mybatis 创建可延迟加载对象所用到的代理工具。            | CGLIB \| JAVASSIST                                           | JAVASSIST （MyBatis 3.3 以上）                        |
| vfsImpl                          | 指定 VFS 的实现                                              | 自定义 VFS 的实现的类全限定名，以逗号分隔。                  | 未设置                                                |
| useActualParamName               | 允许使用方法签名中的名称作为语句参数名称。 为了使用该特性，你的项目必须采用 Java 8 编译，并且加上 `-parameters` 选项。（新增于 3.4.1） | true \| false                                                | true                                                  |
| configurationFactory             | 指定一个提供 `Configuration` 实例的类。 这个被返回的 Configuration 实例用来加载被反序列化对象的延迟加载属性值。 这个类必须包含一个签名为`static Configuration getConfiguration()` 的方法。（新增于 3.2.3） | 一个类型别名或完全限定类名。                                 | 未设置                                                |

一个配置完整的 settings 元素的示例如下：

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="multipleResultSetsEnabled" value="true"/>
  <setting name="useColumnLabel" value="true"/>
  <setting name="useGeneratedKeys" value="false"/>
  <setting name="autoMappingBehavior" value="PARTIAL"/>
  <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
  <setting name="defaultExecutorType" value="SIMPLE"/>
  <setting name="defaultStatementTimeout" value="25"/>
  <setting name="defaultFetchSize" value="100"/>
  <setting name="safeRowBoundsEnabled" value="false"/>
  <setting name="mapUnderscoreToCamelCase" value="false"/>
  <setting name="localCacheScope" value="SESSION"/>
  <setting name="jdbcTypeForNull" value="OTHER"/>
  <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
</settings
```

###### 类型别名

类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

当这样配置时，`Blog` 可以用在任何使用 `domain.blog.Blog` 的地方。

也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean

```xml
<typeAliases>
  <package name="domain.blog"/>
</typeAliases>
```

每一个在包 `domain.blog` 中的 Java Bean，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名。 比如 `domain.blog.Author` 的别名为 `author`；若有注解，则别名为其注解值。

```java
@Alias("author")
public class Author {
    ...
}
```

下面是一些为常见的 Java 类型内建的类型别名。它们都是不区分大小写的，注意，为了应对原始类型的命名重复，采取了特殊的命名风格。

| 别名       | 映射的类型 |
| :--------- | :--------- |
| _byte      | byte       |
| _long      | long       |
| _short     | short      |
| _int       | int        |
| _integer   | int        |
| _double    | double     |
| _float     | float      |
| _boolean   | boolean    |
| string     | String     |
| byte       | Byte       |
| long       | Long       |
| short      | Short      |
| int        | Integer    |
| integer    | Integer    |
| double     | Double     |
| float      | Float      |
| boolean    | Boolean    |
| date       | Date       |
| decimal    | BigDecimal |
| bigdecimal | BigDecimal |
| object     | Object     |
| map        | Map        |
| hashmap    | HashMap    |
| list       | List       |
| arraylist  | ArrayList  |
| collection | Collection |
| iterator   | Iterator   |

###### 类型处理器(TypeHandlers)

MyBatis 在设置预处理语句（PreparedStatement）中的参数或从结果集中取出一个值时， 都会用类型处理器将获取到的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器。

**提示** 从 3.4.5 开始，MyBatis 默认支持 JSR-310（日期和时间 API） 。

| 类型处理器                   | Java 类型                       | JDBC 类型                                                    |
| :--------------------------- | :------------------------------ | :----------------------------------------------------------- |
| `BooleanTypeHandler`         | `java.lang.Boolean`, `boolean`  | 数据库兼容的 `BOOLEAN`                                       |
| `ByteTypeHandler`            | `java.lang.Byte`, `byte`        | 数据库兼容的 `NUMERIC` 或 `BYTE`                             |
| `ShortTypeHandler`           | `java.lang.Short`, `short`      | 数据库兼容的 `NUMERIC` 或 `SMALLINT`                         |
| `IntegerTypeHandler`         | `java.lang.Integer`, `int`      | 数据库兼容的 `NUMERIC` 或 `INTEGER`                          |
| `LongTypeHandler`            | `java.lang.Long`, `long`        | 数据库兼容的 `NUMERIC` 或 `BIGINT`                           |
| `FloatTypeHandler`           | `java.lang.Float`, `float`      | 数据库兼容的 `NUMERIC` 或 `FLOAT`                            |
| `DoubleTypeHandler`          | `java.lang.Double`, `double`    | 数据库兼容的 `NUMERIC` 或 `DOUBLE`                           |
| `BigDecimalTypeHandler`      | `java.math.BigDecimal`          | 数据库兼容的 `NUMERIC` 或 `DECIMAL`                          |
| `StringTypeHandler`          | `java.lang.String`              | `CHAR`, `VARCHAR`                                            |
| `ClobReaderTypeHandler`      | `java.io.Reader`                | -                                                            |
| `ClobTypeHandler`            | `java.lang.String`              | `CLOB`, `LONGVARCHAR`                                        |
| `NStringTypeHandler`         | `java.lang.String`              | `NVARCHAR`, `NCHAR`                                          |
| `NClobTypeHandler`           | `java.lang.String`              | `NCLOB`                                                      |
| `BlobInputStreamTypeHandler` | `java.io.InputStream`           | -                                                            |
| `ByteArrayTypeHandler`       | `byte[]`                        | 数据库兼容的字节流类型                                       |
| `BlobTypeHandler`            | `byte[]`                        | `BLOB`, `LONGVARBINARY`                                      |
| `DateTypeHandler`            | `java.util.Date`                | `TIMESTAMP`                                                  |
| `DateOnlyTypeHandler`        | `java.util.Date`                | `DATE`                                                       |
| `TimeOnlyTypeHandler`        | `java.util.Date`                | `TIME`                                                       |
| `SqlTimestampTypeHandler`    | `java.sql.Timestamp`            | `TIMESTAMP`                                                  |
| `SqlDateTypeHandler`         | `java.sql.Date`                 | `DATE`                                                       |
| `SqlTimeTypeHandler`         | `java.sql.Time`                 | `TIME`                                                       |
| `ObjectTypeHandler`          | Any                             | `OTHER` 或未指定类型                                         |
| `EnumTypeHandler`            | Enumeration Type                | VARCHAR 或任何兼容的字符串类型，用来存储枚举的名称（而不是索引序数值） |
| `EnumOrdinalTypeHandler`     | Enumeration Type                | 任何兼容的 `NUMERIC` 或 `DOUBLE` 类型，用来存储枚举的序数值（而不是名称）。 |
| `SqlxmlTypeHandler`          | `java.lang.String`              | `SQLXML`                                                     |
| `InstantTypeHandler`         | `java.time.Instant`             | `TIMESTAMP`                                                  |
| `LocalDateTimeTypeHandler`   | `java.time.LocalDateTime`       | `TIMESTAMP`                                                  |
| `LocalDateTypeHandler`       | `java.time.LocalDate`           | `DATE`                                                       |
| `LocalTimeTypeHandler`       | `java.time.LocalTime`           | `TIME`                                                       |
| `OffsetDateTimeTypeHandler`  | `java.time.OffsetDateTime`      | `TIMESTAMP`                                                  |
| `OffsetTimeTypeHandler`      | `java.time.OffsetTime`          | `TIME`                                                       |
| `ZonedDateTimeTypeHandler`   | `java.time.ZonedDateTime`       | `TIMESTAMP`                                                  |
| `YearTypeHandler`            | `java.time.Year`                | `INTEGER`                                                    |
| `MonthTypeHandler`           | `java.time.Month`               | `INTEGER`                                                    |
| `YearMonthTypeHandler`       | `java.time.YearMonth`           | `VARCHAR` 或 `LONGVARCHAR`                                   |
| `JapaneseDateTypeHandler`    | `java.time.chrono.JapaneseDate` | `DATE`                                                       |

你可以重写已有的类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。 具体做法为：实现 `org.apache.ibatis.type.TypeHandler` 接口， 或继承一个很便利的类 `org.apache.ibatis.type.BaseTypeHandler`， 并且可以（可选地）将它映射到一个 JDBC 类型。

```java
// ExampleTypeHandler.java
@MappedJdbcTypes(JdbcType.VARCHAR)
public class ExampleTypeHandler extends BaseTypeHandler<String> {

  @Override
  public void setNonNullParameter(PreparedStatement ps, int i, String parameter, JdbcType jdbcType) throws SQLException {
    ps.setString(i, parameter);
  }

  @Override
  public String getNullableResult(ResultSet rs, String columnName) throws SQLException {
    return rs.getString(columnName);
  }

  @Override
  public String getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
    return rs.getString(columnIndex);
  }

  @Override
  public String getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
    return cs.getString(columnIndex);
  }
}
```

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.mybatis.example.ExampleTypeHandler"/>
</typeHandlers>
```

使用上述的类型处理器将会覆盖已有的处理 Java String 类型的属性以及 VARCHAR 类型的参数和结果的类型处理器。 要注意 MyBatis 不会通过检测数据库元信息来决定使用哪种类型，所以你必须在参数和结果映射中指明字段是 VARCHAR 类型， 以使其能够绑定到正确的类型处理器上。这是因为 MyBatis 直到语句被执行时才清楚数据类型。

通过类型处理器的泛型，MyBatis 可以得知该类型处理器处理的 Java 类型，不过这种行为可以通过两种方法改变：

- 在类型处理器的配置元素（typeHandler 元素）上增加一个 `javaType` 属性（比如：`javaType="String"`）；
- 在类型处理器的类上增加一个 `@MappedTypes` 注解指定与其关联的 Java 类型列表。 如果在 `javaType` 属性中也同时指定，则注解上的配置将被忽略。

可以通过两种方式来指定关联的 JDBC 类型：

- 在类型处理器的配置元素上增加一个 `jdbcType` 属性（比如：`jdbcType="VARCHAR"`）；
- 在类型处理器的类上增加一个 `@MappedJdbcTypes` 注解指定与其关联的 JDBC 类型列表。 如果在 `jdbcType` 属性中也同时指定，则注解上的配置将被忽略。

当在 `ResultMap` 中决定使用哪种类型处理器时，此时 Java 类型是已知的（从结果类型中获得），但是 JDBC 类型是未知的。 因此 Mybatis 使用 `javaType=[Java 类型], jdbcType=null` 的组合来选择一个类型处理器。 这意味着使用 `@MappedJdbcTypes` 注解可以*限制*类型处理器的作用范围，并且可以确保，除非显式地设置，否则类型处理器在 `ResultMap` 中将不会生效。 如果希望能在 `ResultMap` 中隐式地使用类型处理器，那么设置 `@MappedJdbcTypes` 注解的 `includeNullJdbcType=true` 即可。 然而从 Mybatis 3.4.0 开始，如果某个 Java 类型**只有一个**注册的类型处理器，即使没有设置 `includeNullJdbcType=true`，那么这个类型处理器也会是 `ResultMap` 使用 Java 类型时的默认处理器。

最后，可以让 MyBatis 帮你查找类型处理器：

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <package name="org.mybatis.example"/>
</typeHandlers>
```

注意在使用自动发现功能的时候，只能通过注解方式来指定 JDBC 的类型。

你可以创建能够处理多个类的泛型类型处理器。为了使用泛型类型处理器， 需要增加一个接受该类的 class 作为参数的构造器，这样 MyBatis 会在构造一个类型处理器实例的时候传入一个具体的类。

```java
//GenericTypeHandler.java
public class GenericTypeHandler<E extends MyObject> extends BaseTypeHandler<E> {
	
  private Class<E> type;

  public GenericTypeHandler(Class<E> type) {
    if (type == null) throw new IllegalArgumentException("Type argument cannot be null");
    this.type = type;
  }
  ...
```

`EnumTypeHandler` 和 `EnumOrdinalTypeHandler` 都是泛型类型处理器.

###### 处理枚举类型

若想映射枚举类型 `Enum`，则需要从 `EnumTypeHandler` 或者 `EnumOrdinalTypeHandler` 中选择一个来使用。

比如说我们想存储取近似值时用到的舍入模式。默认情况下，MyBatis 会利用 `EnumTypeHandler` 来把 `Enum` 值转换成对应的名字。

**注意 `EnumTypeHandler` 在某种意义上来说是比较特别的，其它的处理器只针对某个特定的类，而它不同，它会处理任意继承了 `Enum` 的类。**

不过，我们可能不想存储名字，相反我们的 DBA 会坚持使用整形值代码。那也一样简单：在配置文件中把 `EnumOrdinalTypeHandler` 加到 `typeHandlers` 中即可， 这样每个 `RoundingMode` 将通过他们的序数值来映射成对应的整形数值。

```xml
<!-- mybatis-config.xml -->
<typeHandlers>
  <typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler" javaType="java.math.RoundingMode"/>
</typeHandlers>
```

但要是你想在一个地方将 `Enum` 映射成字符串，在另外一个地方映射成整形值呢？

自动映射器（auto-mapper）会自动地选用 `EnumOrdinalTypeHandler` 来处理枚举类型， 所以如果我们想用普通的 `EnumTypeHandler`，就必须要显式地为那些 SQL 语句设置要使用的类型处理器。

（下一节才开始介绍映射器文件，如果你是首次阅读该文档，你可能需要先跳过这里，过会再来看。）

```xml-dtd
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.apache.ibatis.submitted.rounding.Mapper">
	<resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap">
		<id column="id" property="id"/>
		<result column="name" property="name"/>
		<result column="funkyNumber" property="funkyNumber"/>
		<result column="roundingMode" property="roundingMode"/>
	</resultMap>

	<select id="getUser" resultMap="usermap">
		select * from users
	</select>
	<insert id="insert">
	    insert into users (id, name, funkyNumber, roundingMode) values (
	    	#{id}, #{name}, #{funkyNumber}, #{roundingMode}
	    )
	</insert>

	<resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap2">
		<id column="id" property="id"/>
		<result column="name" property="name"/>
		<result column="funkyNumber" property="funkyNumber"/>
		<result column="roundingMode" property="roundingMode" typeHandler="org.apache.ibatis.type.EnumTypeHandler"/>
	</resultMap>
	<select id="getUser2" resultMap="usermap2">
		select * from users2
	</select>
	<insert id="insert2">
	    insert into users2 (id, name, funkyNumber, roundingMode) values (
	    	#{id}, #{name}, #{funkyNumber}, #{roundingMode, typeHandler=org.apache.ibatis.type.EnumTypeHandler}
	    )
	</insert>

</mapper>
```

注意，这里的 select 语句必须指定 `resultMap` 而不是 `resultType`。

###### 对象工厂（objectFactory）

每次 MyBatis 创建结果对象的新实例时，它都会使用一个对象工厂（ObjectFactory）实例来完成实例化工作。 默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认无参构造方法，要么通过存在的参数映射来调用带有参数的构造方法。 如果想覆盖对象工厂的默认行为，可以通过创建自己的对象工厂来实现。比如：

```java
// ExampleObjectFactory.java
public class ExampleObjectFactory extends DefaultObjectFactory {
  public Object create(Class type) {
    return super.create(type);
  }
  public Object create(Class type, List<Class> constructorArgTypes, List<Object> constructorArgs) {
    return super.create(type, constructorArgTypes, constructorArgs);
  }
  public void setProperties(Properties properties) {
    super.setProperties(properties);
  }
  public <T> boolean isCollection(Class<T> type) {
    return Collection.class.isAssignableFrom(type);
  }}
```

```xml
<!-- mybatis-config.xml -->
<objectFactory type="org.mybatis.example.ExampleObjectFactory">
  <property name="someProperty" value="100"/>
</objectFactory>
```

ObjectFactory 接口很简单，它包含两个创建实例用的方法，一个是处理默认无参构造方法的，另外一个是处理带参数的构造方法的。 另外，setProperties 方法可以被用来配置 ObjectFactory，在初始化你的 ObjectFactory 实例后， objectFactory 元素体中定义的属性会被传递给 setProperties 方法。

###### 环境配置

MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中， 现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射。还有许多类似的使用场景。

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

所以，如果你想连接两个数据库，就需要创建两个 SqlSessionFactory 实例，每个数据库对应一个。而如果是三个数据库，就需要三个实例，依此类推，记起来很简单：

- **每个数据库对应一个 SqlSessionFactory 实例**

为了指定创建哪种环境，只要将它作为可选的参数传递给 SqlSessionFactoryBuilder 即可。可以接受环境配置的两个方法签名是：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, properties);
```

如果忽略了环境参数，那么将会加载默认环境，如下所示：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, properties);
```

environments 元素定义了如何配置环境。

```xml
<environments default="development">
  <environment id="development">
    <transactionManager type="JDBC">
      <property name="..." value="..."/>
    </transactionManager>
    <dataSource type="POOLED">
      <property name="driver" value="${driver}"/>
      <property name="url" value="${url}"/>
      <property name="username" value="${username}"/>
      <property name="password" value="${password}"/>
    </dataSource>
  </environment>
</environments>
```

注意一些关键点:

- 默认使用的环境 ID（比如：default="development"）。
- 每个 environment 元素定义的环境 ID（比如：id="development"）。
- 事务管理器的配置（比如：type="JDBC"）。
- 数据源的配置（比如：type="POOLED"）。

默认环境和环境 ID 顾名思义。 环境可以随意命名，但务必保证默认的环境 ID 要匹配其中一个环境 ID。

**事务管理器**

在 MyBatis 中有两种类型的事务管理器（也就是 type="[JDBC|MANAGED]"）：

- JDBC – 这个配置直接使用了 JDBC 的提交和回滚设施，它依赖从数据源获得的连接来管理事务作用域。

- MANAGED – 这个配置几乎没做什么。它从不提交或回滚一个连接，而是让容器来管理事务的整个生命周期（比如 JEE 应用服务器的上下文）。 默认情况下它会关闭连接。然而一些容器并不希望连接被关闭，因此需要将 closeConnection 属性设置为 false 来阻止默认的关闭行为。例如:

  ```xml
  <transactionManager type="MANAGED">
    <property name="closeConnection" value="false"/>
  </transactionManager>
  ```

**提示** 如果你正在使用 Spring + MyBatis，则没有必要配置事务管理器，因为 Spring 模块会使用自带的管理器来覆盖前面的配置。

这两种事务管理器类型都不需要设置任何属性。它们其实是类型别名，换句话说，你可以用 TransactionFactory 接口实现类的全限定名或类型别名代替它们。

```java
public interface TransactionFactory {
  default void setProperties(Properties props) { // 从 3.5.2 开始，该方法为默认方法
    // 空实现
  }
  Transaction newTransaction(Connection conn);
  Transaction newTransaction(DataSource dataSource, TransactionIsolationLevel level, boolean autoCommit);
}
```

在事务管理器实例化后，所有在 XML 中配置的属性将会被传递给 setProperties() 方法。你的实现还需要创建一个 Transaction 接口的实现类，这个接口也很简单：

```java
public interface Transaction {
  Connection getConnection() throws SQLException;
  void commit() throws SQLException;
  void rollback() throws SQLException;
  void close() throws SQLException;
  Integer getTimeout() throws SQLException;
}
```

使用这两个接口，你可以完全自定义 MyBatis 对事务的处理。

**数据源**

dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

- 大多数 MyBatis 应用程序会按示例中的例子来配置数据源。虽然数据源配置是可选的，但如果要启用延迟加载特性，就必须配置数据源。

有三种内建的数据源类型（也就是 type="[UNPOOLED|POOLED|JNDI]"）：

**UNPOOLED**– 这个数据源的实现会每次请求时打开和关闭连接。虽然有点慢，但对那些数据库连接可用性要求不高的简单应用程序来说，是一个很好的选择。 性能表现则依赖于使用的数据库，对某些数据库来说，使用连接池并不重要，这个配置就很适合这种情形。UNPOOLED 类型的数据源仅仅需要配置以下 5 种属性：

- `driver` – 这是 JDBC 驱动的 Java 类全限定名（并不是 JDBC 驱动中可能包含的数据源类）。
- `url` – 这是数据库的 JDBC URL 地址。
- `username` – 登录数据库的用户名。
- `password` – 登录数据库的密码。
- `defaultTransactionIsolationLevel` – 默认的连接事务隔离级别。
- `defaultNetworkTimeout` – 等待数据库操作完成的默认网络超时时间（单位：毫秒）。查看 `java.sql.Connection#setNetworkTimeout()` 的 API 文档以获取更多信息。

作为可选项，你也可以传递属性给数据库驱动。只需在属性名加上“driver.”前缀即可，例如：

- `driver.encoding=UTF8`

这将通过 DriverManager.getConnection(url, driverProperties) 方法传递值为 `UTF8` 的 `encoding` 属性给数据库驱动。

**POOLED**– 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。 这种处理方式很流行，能使并发 Web 应用快速响应请求。

除了上述提到 UNPOOLED 下的属性外，还有更多属性用来配置 POOLED 的数据源：

- `poolMaximumActiveConnections` – 在任意时间可存在的活动（正在使用）连接数量，默认值：10
- `poolMaximumIdleConnections` – 任意时间可能存在的空闲连接数。
- `poolMaximumCheckoutTime` – 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫秒（即 20 秒）
- `poolTimeToWait` – 这是一个底层设置，如果获取连接花费了相当长的时间，连接池会打印状态日志并重新尝试获取一个连接（避免在误配置的情况下一直失败且不打印日志），默认值：20000 毫秒（即 20 秒）。
- `poolMaximumLocalBadConnectionTolerance` – 这是一个关于坏连接容忍度的底层设置， 作用于每一个尝试从缓存池获取连接的线程。 如果这个线程获取到的是一个坏的连接，那么这个数据源允许这个线程尝试重新获取一个新的连接，但是这个重新尝试的次数不应该超过 `poolMaximumIdleConnections` 与 `poolMaximumLocalBadConnectionTolerance` 之和。 默认值：3（新增于 3.4.5）
- `poolPingQuery` – 发送到数据库的侦测查询，用来检验连接是否正常工作并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动出错时返回恰当的错误消息。
- `poolPingEnabled` – 是否启用侦测查询。若开启，需要设置 `poolPingQuery` 属性为一个可执行的 SQL 语句（最好是一个速度非常快的 SQL 语句），默认值：false。
- `poolPingConnectionsNotUsedFor` – 配置 poolPingQuery 的频率。可以被设置为和数据库连接超时时间一样，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 为 true 时适用）。

**JNDI** – 这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的数据源引用。这种数据源配置只需要两个属性：

- `initial_context` – 这个属性用来在 InitialContext 中寻找上下文（即，initialContext.lookup(initial_context)）。这是个可选属性，如果忽略，那么将会直接从 InitialContext 中寻找 data_source 属性。
- `data_source` – 这是引用数据源实例位置的上下文路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。

和其他数据源配置类似，可以通过添加前缀“env.”直接把属性传递给 InitialContext。比如：

- `env.encoding=UTF8`

这就会在 InitialContext 实例化时往它的构造方法传递值为 `UTF8` 的 `encoding` 属性。

你可以通过实现接口 `org.apache.ibatis.datasource.DataSourceFactory` 来使用第三方数据源实现：

```java
public interface DataSourceFactory {
  void setProperties(Properties props);
  DataSource getDataSource();
}
```

`org.apache.ibatis.datasource.unpooled.UnpooledDataSourceFactory` 可被用作父类来构建新的数据源适配器，比如下面这段插入 C3P0 数据源所必需的代码：

```java
import org.apache.ibatis.datasource.unpooled.UnpooledDataSourceFactory;
import com.mchange.v2.c3p0.ComboPooledDataSource;

public class C3P0DataSourceFactory extends UnpooledDataSourceFactory {

  public C3P0DataSourceFactory() {
    this.dataSource = new ComboPooledDataSource();
  }
}
```

为了令其工作，记得在配置文件中为每个希望 MyBatis 调用的 setter 方法增加对应的属性。 下面是一个可以连接至 PostgreSQL 数据库的例子：

```xml
<dataSource type="org.myproject.C3P0DataSourceFactory">
  <property name="driver" value="org.postgresql.Driver"/>
  <property name="url" value="jdbc:postgresql:mydb"/>
  <property name="username" value="postgres"/>
  <property name="password" value="root"/>
</dataSource>
```

###### 映射器(mapper)

既然 MyBatis 的行为已经由上述元素配置完了，我们现在就要来定义 SQL 映射语句了。 但首先，我们需要告诉 MyBatis 到哪里去找到这些语句。 在自动查找资源方面，Java 并没有提供一个很好的解决方案，所以最好的办法是直接告诉 MyBatis 到哪里去找映射文件。 你可以使用相对于类路径的资源引用，或完全限定资源定位符（包括 `file:///` 形式的 URL），或类名和包名等。例如：

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

这些配置会告诉 MyBatis 去哪里找映射文件，剩下的细节就应该是每个 SQL 映射文件了.

#### XML映射



#### 动态SQL



#### SQL语句构建器



#### 日志



#### Java API



#### 导入依赖

```xml
<!-- MyBatis依赖 -->
<!-- <dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.10</version>
</dependency> -->

<!-- mysql驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- spring整合 -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>

<!-- 用于记录日志 -->
<!-- 添加slf4j日志api -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
</dependency>
<!-- 添加logback-classic依赖 -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
</dependency>
```

日志配置 logback.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--
        CONSOLE ：表示当前的日志信息是可以输出到控制台的。
    -->
    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>[%level] %blue(%d{HH:mm:ss.SSS}) %cyan([%thread]) %boldGreen(%logger{15}) - %msg %n</pattern>
        </encoder>
    </appender>	

    <logger name="com.xxx.xxx" level="DEBUG" additivity="false">
        <appender-ref ref="Console"/>
    </logger>

    <!--
      level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF
     ， 默认debug
      <root>可以包含零个或多个<appender-ref>元素，标识这个输出位置将会被本日志级别控制。
      -->
    <root level="DEBUG">
        <appender-ref ref="Console"/>
    </root>
</configuration>
```

Mapper扫描

```java
@MapperScan(basePackages = "com.example.mybatis.mapper")
public class MybatisApplication {}
```

#### Mapper

resources/xxx.xxx.mapper目录和src/xxx.xxx.mapper相映射

xxxMapper.xml

```xml-dtd
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace 命名空间 -->
<mapper namespace="com.itheima.mapper.BrandMapper">

	<!-- 抽取公共数据
		引入sql片段<include refid=""/>
	-->
    <sql id="brand_column">

    </sql>

    <!--
        id: 唯一标识
        type：映射的类型，支持别名
    -->
    <resultMap id="brandResultMap" type="brand">
        <!--
            id 完成主键字段的映射
                column 表的别名
                property 类的字段名
            result 完成一般字段的映射
        -->
        <result column="brand_name" property="brandName"/>
        <result column="company_name" property="companyName"/>
    </resultMap>

    <select id="selectAll" resultMap="brandResultMap">
        select *
        from tb_brand;
    </select>

    <select id="selectById" resultMap="brandResultMap">
        select *
        from tb_brand
        where id = #{id};
    </select>
    <!--<select id="selectByCondition" resultMap="brandResultMap">
        select *
        from tb_brand
        where status = #{status}
        and company_name like #{companyName}
        and brand_name like #{brandName}
    </select>-->
    <select id="selectByCondition" resultMap="brandResultMap">
        select *
        from tb_brand
        <where>
            <if test="status != null">
                and status = #{status}
            </if>
            <if test="companyName != null and companyName != '' ">
                and company_name like #{companyName}
            </if>
            <if test="brandName != null and brandName != '' ">
                and brand_name like #{brandName}
            </if>
        </where>

    </select>
    <!--<select id="selectByConditionSingle" resultMap="brandResultMap">
        select *
        from tb_brand
        where
        <choose>
            <when test="status != null">
                status = #{status}
            </when>
            <when test="companyName != null and companyName != '' ">
                company_name like #{companyName}
            </when>
            <when test="brandName != null and brandName != '' ">
                brand_name like #{brandName}
            </when>
            <otherwise>
                1 = 1
            </otherwise>
        </choose>
    </select>-->

    <select id="selectByConditionSingle" resultMap="brandResultMap">
        select *
        from tb_brand
        <where>
            <choose>
                <when test="status != null">
                    status = #{status}
                </when>
                <when test="companyName != null and companyName != '' ">
                    company_name like #{companyName}
                </when>
                <when test="brandName != null and brandName != '' ">
                    brand_name like #{brandName}
                </when>
            </choose>
        </where>
    </select>

	<!-- 插入后返回主键值 -->
    <insert id="add" useGeneratedKeys="true" keyProperty="id">
        insert into tb_brand (brand_name, company_name, ordered, description, status)
        VALUES (#{brandName}, #{companyName}, #{ordered}, #{description}, #{status});
    </insert>

    <!--<update id="update">
        update tb_brand
        set brand_name = #{brandName},
            company_name = #{companyName},
            ordered = #{ordered},
            description = #{description},
            status = #{status}
        where id = #{id};
    </update>-->

    <update id="update">
        update tb_brand
        <set>
            <if test="brandName != null and brandName != '' ">
                brand_name = #{brandName},
            </if>
            <if test="companyName != null and companyName != '' ">
                company_name = #{companyName},
            </if>
            <if test="ordered != null">
                ordered = #{ordered},
            </if>
            <if test="description != null and description != '' ">
                description = #{description},
            </if>
            <if test="status != null">
                status = #{status}
            </if>
        </set>
        where id = #{id};
    </update>

    <delete id="deleteById">
        delete
        from tb_brand
        where id = #{id};
    </delete>

    <delete id="deleteByIds">
        delete
        from tb_brand
        where id
        in
        <foreach collection="ids" item="id" separator="," open="(" close=")">
            #{id}
        </foreach>
        ;
    </delete>

</mapper>
```

多表查询

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mybatis.mapper.EmpMapper">

    <!-- 结果映射 id：唯一标识，type：实体类类名，指定返回类型 -->
    <resultMap id="empMap" type="emp">
        <!--    
			设置对应关系  column：数据库中的列，property: 实体类属性
  			javaType：实体属性类型，jdbcType：数据库中的字段类型
		-->
        <result column="username" property="username" javaType="String" jdbcType="VARCHAR"/>
        <!--
 			collection: 收集数据，select：指定需要调用的sql id，
			调用selectSex查询数据，并将gender作为selectSex作为id参数进行传入
		-->
        <collection property="gender" javaType="string" column="id" select="selectSex"/>
    </resultMap>

    <!-- autoMapping: 自动映射（column和property相同时） -->
    <resultMap id="orderMap" type="order" autoMapping="true">
        <!--<id column="id" property="id"/>
        <result column="uid" property="uid"/>
        <result column="total" property="total"/>-->
        <result column="create_at" property="createAt"/>
        <!--<result column="username" property="user.username"/>-->
        <!-- 关联数据，将关联表查询数据，关联给user属性（适用于1对1或多对1） -->
        <association property="user" javaType="user" autoMapping="true">
            <id column="uid" property="id"/>
        </association>
    </resultMap>

    <resultMap id="userMap" type="user" autoMapping="true">
        <!-- 收集关联表的数据，将查询到的集合指定ofType的类型，并赋值给（使用与1对多或多对多） -->
        <collection property="orders" ofType="order" autoMapping="true">
            <id column="oid" property="id"/>
            <result column="create_at" property="createAt"/>
        </collection>
    </resultMap>

    <!-- 启用继承extend -->
    <resultMap id="userRoleMap" type="user" extends="userMap" autoMapping="true">
        <collection property="roles" ofType="role" autoMapping="true">
        </collection>
    </resultMap>

    <!-- parameterType 入参类型，指定resultMap 结果映射 -->
    <select parameterType="int" id="selectById" resultMap="empMap">
        select *
        from tb_emp
        where id = #{id}
    </select>

    <!-- resultType：返回结果类型 -->
    <select id="selectSex" parameterType="int" resultType="string">
        select name
        from tb_sex
        where id = #{id}
    </select>

    <select id="getOrders" resultMap="orderMap">
        select *
        from tb_order o,
             tb_user u
        where o.id = u.id
    </select>

    <select id="getUsers" resultMap="userMap">
        select *, o.id oid
        from tb_user
                 left join tb_order o on tb_user.id = o.uid
    </select>

    <select id="getUsersAndRole" resultMap="userRoleMap">
        /*select *
        from tb_user u
                 left join tb_order o on u.id = o.uid
                 left join tb_user_role ur on ur.user_id = u.id
                 inner join tb_role r on r.id = ur.role_id*/
        select * from tb_user u, tb_user_role ur, tb_role r where u.id = ur.user_id and ur.role_id = r.id
    </select>
</mapper>
```

对应类xxxMapper.java

```java
public interface BrandMapper {

    List<Brand> selectAll();

    // 映射
    @Results(
        {
            @Result(column = "", property = "")
        }
    )
    Brand selectById(int id);

   /* List<Brand> selectByCondition(@Param("status") int status,
                                  @Param("companyName") String companyName,
                                  @Param("brandName") String brandName);*/

   /* List<Brand> selectByCondition(Brand brand);*/

    // Map参数
    List<Brand> selectByCondition(Map<String, String> map);

    // 对象参数
    List<Brand> selectByConditionSingle(Brand brand);

    // 返回自增的主键值
    @Options(keyProperty = "id", usegeneratedKeys = true)
    int add(Brand brand);

    int update(Brand brand);

    void deleteById(int id);
    // 数组参数
    void deleteByIds(@Param("ids") int[] ids);
}
```

> 安装插件 MyBatisX

具体实现

```java
@Test
public void testSelectAll() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession();
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    List<Brand> brands = brandMapper.selectAll();
    System.out.println("brands = " + brands);

    sqlSession.close();

}

@Test
public void testSelectById() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession();
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    Brand brand = brandMapper.selectById(1);
    System.out.println("brand = " + brand);

    sqlSession.close();
}

@Test
public void testSelectByCondition() throws IOException {

    int status = 1;
    String companyName = "华为";
    String brandName = "华为";

    companyName = "%" + companyName + "%";

    /*Brand brand = new Brand();
    brand.setStatus(status);
    brand.setCompanyName(companyName);
    brand.setBrandName(brandName);*/

    Map<String, String> map = new HashMap<>();
    // map.put("status", String.valueOf(status));
    map.put("companyName", companyName);
    // map.put("brandName", brandName);

    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession();
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    List<Brand> brands = brandMapper.selectByCondition(map);
    System.out.println("brand = " + brands);

    sqlSession.close();
}

@Test
public void testSelectByConditionSingle() throws IOException {

    int status = 1;
    String companyName = "华为";
    String brandName = "华为";

    companyName = "%" + companyName + "%";

    Brand brand = new Brand();
    //  brand.setStatus(status);
    // brand.setCompanyName(companyName);
    // brand.setBrandName(brandName);

   /* Map<String, String> map = new HashMap<>();
    // map.put("status", String.valueOf(status));
    map.put("companyName", companyName);
    // map.put("brandName", brandName);*/

    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession();
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    List<Brand> brands = brandMapper.selectByConditionSingle(brand);
    System.out.println("brand = " + brands);

    sqlSession.close();
}

@Test
public void testAdd() throws IOException {

    int status = 1;
    String companyName = "波导手机";
    String brandName = "波导";

    Brand brand = new Brand();
    brand.setStatus(status);
    brand.setCompanyName(companyName);
    brand.setBrandName(brandName);
    brand.setOrdered(10);
    brand.setDescription("手机中的战斗机");

    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    brandMapper.add(brand);

    Integer id = brand.getId();

    System.out.println("id = " + id);

    // 提交事务
    // sqlSession.commit();

    sqlSession.close();
}

@Test
public void testUpdate() throws IOException {

    int status = 1;
    String companyName = "波导手机";
    String brandName = "波导";

    Brand brand = new Brand();
    brand.setId(5);
    brand.setStatus(0);
//        brand.setCompanyName(companyName);
//        brand.setBrandName(brandName);
//        brand.setOrdered(200);
//        brand.setDescription("波导手机，手机中的战斗机");

    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    int row = brandMapper.update(brand);

    Integer id = brand.getId();

    System.out.println("id = " + id + ", row = " + row);

    // 提交事务
    // sqlSession.commit();

    sqlSession.close();
}

@Test
public void testDeleteById() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    brandMapper.deleteById(6);

    sqlSession.close();
}

@Test
public void testDeleteByIds() throws IOException {
    InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession(true);
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    brandMapper.deleteByIds(new int[]{5, 7, 8});

    sqlSession.close();
}
```

### MybatisPlus

##### 支持分页功能

配置Bean

```java
package com.example.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MpConfig {

    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mybatisPlusInterceptor;
    }

}
```

具体实现

```java
package com.example.service;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.service.IService;
import com.example.domain.Book;

public interface IBookService extends IService<Book>  {

    IPage<Book> getPage(int currentPage, int pageSize);

}


package com.example.service.impl;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.baomidou.mybatisplus.extension.service.impl.ServiceImpl;
import com.example.domain.Book;
import com.example.mapper.BookMapper;
import com.example.service.IBookService;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
public class BookServiceImpl2 extends ServiceImpl<BookMapper, Book> implements IBookService {

    @Resource
    private BookMapper bookMapper;

    @Override
    public IPage<Book> getPage(int currentPage, int pageSize) {
        // selectPage方法实现分页效果
        return bookMapper.selectPage(new Page<>(currentPage, pageSize), null);
    }
}
```

数据层

```java
@Mapper
public interface BookDao {

    // 插入数据
    @Insert("insert into book (type, name, description) values (#{type}, #{name}, #{description})")
    void save(Book book);

    // 更新数据
    @Update("update book set type = #{type}, name = #{name}, description = #{description} where id = #{id}")
    void update(Book book);

    // 删除数据
    @Delete("delete from book where id = #{id}")
    void delete(Integer id);

    // 查询数据
    @Select("select * from book where id = #{id}")
    Book getById(Integer id);

    @Select("select * from book")
    List<Book> getAll();

}
```

业务层

```java
@RestController
@RequestMapping("/books")
public class BookController {

    @Autowired
    private BookService bookService;

    @PostMapping
    public Result save(@RequestBody Book book) {
        boolean flag = bookService.save(book);
        return new Result(flag ? Code.SAVE_OK : Code.SAVE_ERR, flag);
    }

    @PutMapping
    public Result update(@RequestBody Book book) {
        boolean flag = bookService.update(book);
        return new Result(flag ? Code.UPDATE_OK : Code.UPDATE_ERR, flag);
    }

    @DeleteMapping("/{id}")
    public Result delete(@PathVariable Integer id) {
        boolean flag = bookService.delete(id);
        return new Result(flag ? Code.DELETE_OK : Code.DELETE_ERR, flag);
    }

    @GetMapping("/{id}")
    public Result getById(@PathVariable Integer id) {
        // int i = 1 / 0;

        Book book = bookService.getById(id);
        Integer code = book != null ? Code.GET_OK : Code.GET_ERR;
        String msg = book != null ? "查询成功" : "数据查询失败";
        return new Result(code, book, msg);
    }

    @GetMapping
    public Result getAll() {
        List<Book> bookList = bookService.getAll();
        Integer code = bookList != null ? Code.GET_OK : Code.GET_ERR;
        String msg = bookList != null ? "查询成功" : "数据查询失败";
        return new Result(code, bookList, msg);
    }

}
```

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

## SpringBoot

#### 配置

application.yml

```yml
server:
	port: 80 #配置服务端口
```

```yml
name: 123 # 配置name属性

# 数组格式
likes:
  - name
  - age
# map
users:
  -
    name: zhansan
    age: 14
  -
    name: lisi
    age: 15

baseDir: C:\windows

# 使用 ${属性名}的方式引用数据
tempDir: "${baseDir}\temp"

# 配置数据库信息
datasource:
  username: root
  password: 1234
  url: jdbc:mysql://localhost:3306
```

读取配置文件

```java
package com.example.pojo;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 *  封装yml文件中的数据
 */
@Component // 声明bean注解，加入ioc容器中，如果使用@EnableConfigurationProperties注解则无需此注解
// 配置读取配置文件的前缀
@ConfigurationProperties(prefix = "datasource")
public class MyDataSource {

    // 与配置文件中的属性名相对应
    private String username;
    private String password;
    private String url;

    @Override
    public String toString() {
        return "MyDataSource{" +
                "username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", url='" + url + '\'' +
                '}';
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }
}
```

开启配置

```java
@EnableConfigurationProperties(XXX.class)
public class SpringbootWebQuickstartApplication {}
```

在其他位置读取配置文件

```java
@RestController
@RequestMapping("/books")
public class BookController {

    // @Value获取
    /*@Value("${lesson}")
    private String lesson;

	// 获取数组中的第一个
    @Value("${enterprise.subject[0]}")
    private String subject;

	// 使用此对象获取配置
    @Autowired
    private Environment environment;

	// 直接使用配置对象
    @Autowired
    private Enterprise enterprise;*/

    @GetMapping("/{id}")
    public String getById(@PathVariable Integer id) {
        /*System.out.println(subject);
        // 获取到对应的属性
        System.out.println(environment.getProperty("lesson"));
        System.out.println(enterprise);*/
        return String.valueOf(id);
    }
}
```

##### 参数校验

application.yml

```yml
servers:
  ipAddress: 192.168.0.1
  port: 8888
  timeout: 1000
  serverTimeout: 3
  dataSize: 10GB
datasource:
  driverClassName: com.mysql.driver
  password: 0127

spring:
  datasource:
    hikari:
      username: root
      jdbc-url: jdbc:mysql://localhost:3306/db1?useSSL=false
#    tomcat:
#      username: root
#      max-active: 1000
```

添加依赖

```xml
<!-- 生成配置文件元数据 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
    <!-- 校验器 -->
    <dependency>
        <groupId>org.hibernate.validator</groupId>
        <artifactId>hibernate-validator</artifactId>
    </dependency>
    <!-- 数据校验 -->
    <!--<dependency>
        <groupId>javax.validation</groupId>
        <artifactId>validation-api</artifactId>
    </dependency>-->
```

实体类

```java
package com.example.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.convert.DurationUnit;
import org.springframework.util.unit.DataSize;
import org.springframework.validation.annotation.Validated;

import javax.validation.constraints.Max;
import javax.validation.constraints.Min;
import java.time.Duration;
import java.time.temporal.ChronoUnit;

// @Component // 声明bean注解，加入ioc容器中，如果使用@EnableConfigurationProperties注解则无需此注解
@Data // lombok
@ConfigurationProperties(prefix = "servers")
// 2.开启当前bean的数据校验
@Validated
public class ServerConfig {

    private String ipAddress;
    @Max(value = 8888, message = "最大值不能超过8888")
    @Min(value = 222, message = "最小值不能小于222")
    private int port;
    private long timeout;

    @DurationUnit(ChronoUnit.HOURS)
    private Duration serverTimeOut;

    //    @DataSizeUnit(DataUnit.KILOBYTES)
    private DataSize dataSize;

}
```

注入到bean中，配置文件中的数据将自动设置到bean中

```java
@Bean
@ConfigurationProperties(prefix = "datasource")
public DruidDataSource druidDataSource() {
    DruidDataSource druidDataSource = new DruidDataSource();
    // druidDataSource.setDriverClassName("com.mysql.jdbc.Driver");
    return druidDataSource;
}
```

##### 在测试环境中

```yml
# application.yml
# test:
#  prop: testValue
#
testcase:
  book:
    id: ${random.int(0, 100)}
```

```java
// @Configuration // 如果使用了导入的方式，则无需使用此注解
public class MsgConfig {

    @Bean
    public String msg() {
        return "bean msg";
    }

}
```

```java
@SpringBootTest
// 导入
@Import(MsgConfig.class)

public class ConfigurationTest {

    @Resource
    private String msg;

    @Test
    void test() {
        System.out.println(msg);
    }

}
```

临时配置

```java
//临时的属性配置
//@SpringBootTest(properties = {"test.prop=testValue1"})
//@SpringBootTest(args = {"--test.prop=testValue2"})
@SpringBootTest(properties = {"test.prop=testValue1"}, args = {"--test.prop=testValue2"})
class Springboot4TestApplicationTests {

    @Value("${test.prop}")
    private String msg;

    @Test
    void contextLoads() {
        System.out.println(msg);
    }
}
```

web环境模拟请求测试

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
// 开启虚拟MVC调用
@AutoConfigureMockMvc
public class WebTest {

    @Test
    void test(@Autowired MockMvc mockMvc) throws Exception {
        // 创建了一个虚拟的请求
        MockHttpServletRequestBuilder builder = MockMvcRequestBuilders.get("/books");
        ResultActions resultActions = mockMvc.perform(builder);

        // 定义本次调用的预期值
        /*StatusResultMatchers status = MockMvcResultMatchers.status();
        ResultMatcher ok = status.isOk();*/

        // 验证响应体
        /*ContentResultMatchers content = MockMvcResultMatchers.content();
        // ResultMatcher string = content.string("springboot1");
        ResultMatcher json = content.json(
                "{\"id\": 1,\"name\": \"springboot1\", \"type\": \"spring\"}"
        );*/

        HeaderResultMatchers header = MockMvcResultMatchers.header();
        ResultMatcher matcher = header.string("Content-Type", "application/json");

        // 添加本次预计值与本次调用过程进行匹配
        resultActions.andExpect(matcher);
    }
}
```

#### 环境配置

根据环境配置不同变量，引用方式@变量@

```xml
<!-- 配置环境 -->
<profiles>
    <profile>
        <id>dev</id>
        <properties>
            <profile.active>dev</profile.active>
        </properties>
    </profile>
    <profile>
        <id>pro</id>
        <properties>
            <profile.active>pro</profile.active>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
</profiles>

<build>
    <!-- 设置需要打包的资源 -->
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <excludes>
                <exclude>*.yml</exclude>
            </excludes>
            <includes>
                <include>application.yml</include>
                <include>application-${profile.active}.yml</include>
            </includes>
        </resource>
    </resources>
</build>
```

application.yml

根据不同环境选择不同的application-xxx.yml配置

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db01?serverTimezone=UTC
    username: root
    password: jdy200255
  profiles:
    active: @profile.active@ # 当前的环境
mybatis:
  mapper-locations: classpath:mapper/*xml
  type-aliases-package: com.example.mybatis.pojo
  configuration:
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl
logging:
  level:
    root: info
  config: classpath:logback.xml
  file:
    name: server.log
server:
  port: 90
```

#### @RestController

请求处理类，@Contoller与@ResponseBody结合

#### @ResponseBody

响应json数据

#### @RequestBody

接收json数据(请求体数据)

#### @RequestMapping

请求映射

#### @GetMapping

GET映射

#### @PostMapping

POST映射

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
public String dateParam(@DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss") LocalDa	teTime updateTime) {
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

### Bean加载

#### @Bean

```java
// 默认将加载
@Bean
public Cat tom() {
 	return new Cat();
}
```

#### @ConditionalOnClass

@ConditionalOnClass(Mouse.class) 当容器中有Mouse这个bean才会加载

```java
@Bean
@ConditionalOnClass(Mouse.class)
public Cat tom() {
    return new Cat();
}
```

#### @ConditionalOnMissingClass

当容器中没有某个类时才会加载

具体使用

```java
@Bean
@ConditionalOnClass(name = "com.mysql.cj.jdbc.Driver")
public DruidDataSource druidDataSource() {
    return new DruidDataSource();
}
```

#### @Configuration

配置项，集中对Bean进行配置，此注解包含@Component注解

```java
// 配置此注解将注入到容器中，无需import
@Configuration // 也可使用import或@Component注解注入
public class DBConfig {

    // 配置Bean
    @Bean
    public DruidDataSource druidDataSource() {
        return new DruidDataSource();
    }
}
```

#### 动态加载

动态加载配置项

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
String[] names = ctx.getBeanDefinitionNames();
for (String name : names) {
    System.out.println("name = " + name);
}
System.out.println(ctx.getBean("dog"));
```

#### @ComponentScan

组件扫描

```java
@ComponentScan({"com.example.adminclient.bean"})
public class SpringConfig {}
```

#### @Import

@import(xxxx.class) 导入指定类到容器中

```java
@Import({Dog.class, DbConfig.class})
public class SpringConfig4 {}
```

#### ImportSelector

定义选择器

```java
public class MyImportSelector implements ImportSelector {
    @Override
    public String[] selectImports(AnnotationMetadata metadata) {
        String className = metadata.getClassName();
        System.out.println("className = " + className);
        boolean hasAnnotation = metadata.hasAnnotation("org.springframework.context.annotation.Configuration");
        System.out.println("hasAnnotation = " + hasAnnotation);
        // 返回指定的类路径
        return new String[]{"com.itheima.bean.Dog", "com.itheima.bean.Cat"};
    }
}

```

导入到容器中

```java
@Configuration
@Import({MyImportSelector.class})
public class SpringConfig6 {}
```

#### ImportBeanDefinitionRegistrar

注册Bean

```java
public class MyRegistrar implements ImportBeanDefinitionRegistrar {
    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
        BeanDefinition beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(BookServiceImpl2.class).getBeanDefinition();
        registry.registerBeanDefinition("bookService", beanDefinition);
    }
}
```

导入

```java
@Configuration
@Import({MyRegistrar.class})
public class SpringConfig7 {}
```

#### PostProcessor

```java
public class MyPostProcessor implements BeanDefinitionRegistryPostProcessor {

    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry beanDefinitionRegistry) throws BeansException {
        BeanDefinition beanDefinition = BeanDefinitionBuilder.rootBeanDefinition(BookServiceImpl4.class).getBeanDefinition();
        beanDefinitionRegistry.registerBeanDefinition("bookService", beanDefinition);
    }

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory configurableListableBeanFactory) throws BeansException {

    }
}
```

#### @ImportResource

加载配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <!-- Xml申明自己开发的Bean -->
    <bean id="cat" class="com.itheima.bean.Cat"/>
    <bean class="com.itheima.bean.Dog"/>

    <!-- 申明第三方bean -->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"/>
</beans>
```

#### Bean 生命周期

```java
// 自定义初始化
@PostConstruct
public void springPostConstruct(){
    System.out.println("@PostConstruct");
}

// 自定义销毁
@PreDestroy
public void springPreDestory(){
    System.out.println("@PreDestory");
}
```

##### BeanNameAware

```java
public class Book implements BeanNameAware {
    public void setBeanName(String name) {
    	System.out.println("Book.setBeanName invoke");
    }
}
```

##### BeanFactoryAware

```java
public class Book implements BeanNameAware {
    public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
        System.out.println("Book.setBeanFactory invoke");
    }
}
```

##### InitializingBean

#### 加载Bean

```java
//@Component // 如果声明此注解，则无需使用@import进行导入
@Data
@EnableConfigurationProperties(CartoonProperties.class)
// 实现 ApplicationContextAware 接口，可以获取到applicationContext
public class CartoonCatAndMouse implements ApplicationContextAware {

    private Cat cat;

    private Mouse mouse;

    // 配置文件，这里使用构造器方法进行引入
    private CartoonProperties cartoonProperties;

    public CartoonCatAndMouse(CartoonProperties cartoonProperties) {
        this.cartoonProperties = cartoonProperties;
        cat = new Cat();
        cat.setName(StringUtils.hasText(cartoonProperties.getCat().getName()) ? cartoonProperties.getCat().getName():  "Tom");
        cat.setAge(3);
        mouse = new Mouse();
        mouse.setAge(4);
        mouse.setName("Jerry");
    }

    public void play() {
        String[] names = applicationContext.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println("name = " + name);
        }
        System.out.println(cat.getAge() + "岁的" + cat.getName() + "和"
                + mouse.getAge() + "岁的" + mouse.getName() + "打起来了");
    }

    private ApplicationContext applicationContext;

    // 重写此方法以获取到ApplicationContext
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```

导入到容器中

```java
@SpringBootApplication
// 对上面的配置进行导入到容器中
@Import(CartoonCatAndMouse.class)
public class App {
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(App.class, args);
        // 获取到容器中指定的bean
        CartoonCatAndMouse bean = context.getBean(CartoonCatAndMouse.class);
        // 调用bean的方法
        bean.play();
        System.out.println("bean = " + bean);
    }
}
```

#### SpringBeanUtils

获取Bean工具类

```java
@Component
public class SpringBeanUtils implements ApplicationContextAware {

    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(@NonNull ApplicationContext applicationContext) throws BeansException {
        if (SpringBeanUtils.applicationContext == null) {
            SpringBeanUtils.applicationContext = applicationContext;
        }
    }

    public static Object getBean(String name) {
        return applicationContext.getBean(name);
    }

    public static <T> T getBean(Class<T> clazz) {
        return applicationContext.getBean(clazz);
    }
}
```

### 异常处理

异常处理类

```java
public class SystemException extends RuntimeException {

    private Integer code;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public SystemException(Integer code, String message) {
        super(message);
        this.code = code;
    }

    public SystemException(Integer code, String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }
}
```

具体实现

```java
/**
 *  异常处理类
 */
 @RestControllerAdvice
public class ProjectExceptionAdvice {

     // 处理系统异常
    @ExceptionHandler(SystemException.class)
    public Result doSystemException(SystemException e) {
        // 记录日志
        // 发送信息给运维
        // 发送邮件给开发人员
        return new Result(e.getCode(),  e.getMessage());
    }

    // 处理业务异常
    @ExceptionHandler(BusinessException.class)
    public Result doBusinessException(BusinessException e) {
        return new Result(e.getCode(),  e.getMessage());
    }

    // 拦截其他异常
    @ExceptionHandler(Exception.class)
    public Result doException(Exception exception) {
        return new Result(Code.SYSTEM_UNKNOW_ERR,  "嘿嘿出异常了");
    }
}
```

### 日志

导入坐标 lombok

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

获取日志对象

```java
// 创建日志记录对象
private static final Logger log = LoggerFactory.getLogger(BookController.class);
```

lombok注解获取

```java
@Slf4j
@RestController
@RequestMapping("/books")
public class BookController {}
```

具体使用

```java
@GetMapping
public String getById() {
    System.out.println("springboot is running...");

    log.debug("debug...");
    log.info("info...");
    log.warn("warn...");
    log.error("error...");

    return "springboot is running...";
}
```

配置文件application.properties

```properties
# 应用名称
spring.application.name=springboot-1-log
# 应用服务 WEB 访问端口
server.port=8080

# 开启调试
# debug=true

# 设置日志级别
logging.level.root=info
# 设置某个包的日志级别
# logging.level.com.example.controller=debug

# 设置分组对某个组设置日志级别
# 设置分组
logging.group.ebank=com.example.controller,com.example.service
logging.group.iservice=com.example

# 设置对应分组的日志级别
logging.level.ebank=debug

# 控制台输出格式
# logging.pattern.console=%d - %m %n
# %d 日期
# %m 输出信息
# %n 换行
# logging.pattern.console=%d %clr(%5p) %n
# %p 日志级别
# clr 彩色显示
#logging.pattern.console=%d %clr(%5p) --- [%16t] %n
# %t 线程名
#logging.pattern.console=%d %clr(%5p) --- [%16t] %40c %n
# %c 类名
#logging.pattern.console=%d %clr(%5p) --- [%16t] %clr(%-40.40c){cyan} : %m %n

# 日志文件名
logging.file.name=server.log

# 日志最大存储大小
logging.logback.rollingpolicy.max-file-size=4KB
# 日志命名规范
logging.logback.rollingpolicy.file-name-pattern=server.%d{yyyy-MM-dd}.%i.log
```

### 热部署

导入坐标

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

打开Idea设置，找到Compiler，勾选Build project automatically和compile independent module

按ctrl + shift + alt + /，点击registry，勾选compiler.automake.allow.when.app.running

修改启动设置，把on update action he on frame deactivation 都设置为"update classes and resources"

关闭热部署

```java
public static void main(String[] args) {
   	System.setProperty("spring.devtools.restart.enabled", "false");
    SpringApplication.run(Application.class, args);
}
```

### Redis

添加依赖

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```

配置

```yml
spring:
  redis:
    client-type: lettuce # 客户端类型
    lettuce:
      pool:
        max-active: 16
    jedis:
      pool:
        max-active: 16
```

使用

```java
@Autowired
private RedisTemplate redisTemplate;

@Test
void contextLoads() {
}

@Test
void set() {
    ValueOperations ops = redisTemplate.opsForValue();
    ops.set("age", 41);
}

@Test
void get() {
    ValueOperations ops = redisTemplate.opsForValue();
    Object age = ops.get("name");
    System.out.println("name = " + age);
}
@Test
void hSet() {
    HashOperations ops = redisTemplate.opsForHash();
    ops.put("info", "a", "aa");
}

@Test
void hGet() {
    HashOperations ops = redisTemplate.opsForHash();
    Object a = ops.get("info", "a");
    System.out.println("a = " + a);
}
```

StringRedisTemplate

```java
@Autowired
private StringRedisTemplate stringRedisTemplate;

@Test
void get() {
    ValueOperations<String, String> ops = stringRedisTemplate.opsForValue();
    String name = ops.get("name");
    System.out.println("name = " + name);
}
```

### Mongodb

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

配置

```yml
spring:
  data:
    mongodb:
      uri: mongodb://localhost/itheima
```

使用

```java
@Autowired
private MongoTemplate mongoTemplate;

@Test
void contextLoads() {
    Book book = new Book();
    book.setId(2);
    book.setName("springboot2");
    book.setType("springboot2");
    book.setDescription("springboot2");
    mongoTemplate.save(book);
}

@Test
void find() {
    List<Book> books = mongoTemplate.findAll(Book.class);
    System.out.println("books = " + books);
}
```

### Elasticsearch

添加依赖

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
</dependency>
```

使用

```java
@Autowired
private BookDao bookDao;

/*@Autowired
private ElasticsearchRestTemplate restTemplate;*/

@Autowired
private RestHighLevelClient restHighLevelClient;

@BeforeEach
void setUp() {
    /*HttpHost host = HttpHost.create("http://localhost:9200");
    RestClientBuilder builder = RestClient.builder(host);
    restHighLevelClient = new RestHighLevelClient(builder);*/
}

@AfterEach
void tearDown() throws IOException {
    restHighLevelClient.close();
}

@Test
void contextLoads() throws IOException {

    /*Book book = bookDao.selectById(3);
    System.out.println("book = " + book);*/

    IndicesClient indices = restHighLevelClient.indices();
    CreateIndexRequest request = new CreateIndexRequest("books");
    // 设置请求中的参数
    String json = "{\n" +
            "    \"mappings\": {\n" +
            "        \"properties\": {\n" +
            "            \"id\": {\n" +
            "                \"type\": \"keyword\"\n" +
            "            },\n" +
            "            \"name\": {\n" +
            "                \"type\": \"text\",\n" +
            "                \"analyzer\": \"ik_max_word\",\n" +
            "                \"copy_to\": \"all\"\n" +
            "            },\n" +
            "            \"type\": {\n" +
            "                \"type\": \"keyword\"\n" +
            "            },\n" +
            "            \"description\": {\n" +
            "                \"type\": \"text\",\n" +
            "                \"analyzer\": \"ik_max_word\",\n" +
            "                \"copy_to\": \"all\"\n" +
            "            },\n" +
            "            \"all\": {\n" +
            "                \"type\": \"text\",\n" +
            "                \"analyzer\": \"ik_max_word\"\n" +
            "            }\n" +
            "        }\n" +
            "    }\n" +
            "}";
    request.source(json, XContentType.JSON);
    indices.create(request, RequestOptions.DEFAULT);
}

@Test
void testCreateDoc() throws IOException {
    Book book = bookDao.selectById(3);
    IndexRequest request = new IndexRequest("books").id(String.valueOf(book.getId()));
    String json = JSON.toJSONString(book);
    request.source(json, XContentType.JSON);
    restHighLevelClient.index(request, RequestOptions.DEFAULT);
}

/**
 * 批处理
 * @throws IOException
 */
@Test
void testCreateAllDoc() throws IOException {
    List<Book> bookList = bookDao.selectList(null);
    /*IndexRequest request = new IndexRequest("books").id(String.valueOf(book.getId()));
    String json = JSON.toJSONString(book);
    request.source(json, XContentType.JSON);
    restHighLevelClient.index(request, RequestOptions.DEFAULT);*/

    BulkRequest bulkRequest = new BulkRequest();

    for (Book book : bookList) {
        IndexRequest request = new IndexRequest("books").id(String.valueOf(book.getId()));
        String json = JSON.toJSONString(book);
        request.source(json, XContentType.JSON);
        bulkRequest.add(request);
    }

    restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
}

@Test
void testGet() throws IOException {
    GetRequest request = new GetRequest("books", "3");
    GetResponse response = restHighLevelClient.get(request, RequestOptions.DEFAULT);

    String source = response.getSourceAsString();
    System.out.println("source = " + source);
}

@Test
void testSearch() throws IOException {
    SearchRequest request = new SearchRequest("books");
    SearchSourceBuilder builder = new SearchSourceBuilder();
    builder.query(QueryBuilders.termQuery("name", "spring"));
    request.source(builder);
    SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);
    SearchHits hits = response.getHits();
    for (SearchHit hit : hits) {
        String source = hit.getSourceAsString();
        // System.out.println("source = " + source);
        Book book = JSON.parseObject(source, Book.class);
        System.out.println("book = " + book);
    }
}
```

### 缓存

开启缓存

```java
@SpringBootApplication
// 开启缓存
@EnableCaching
public class Application {}
```

#### Memcached

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>com.googlecode.xmemcached</groupId>
    <artifactId>xmemcached</artifactId>
    <version>2.4.7</version>
</dependency>
```

配置

```yml
memcached:
  servers: localhost:11211
  poolSize: 10
  opTimeOut: 3000
```

使用

```java
@Service
public class SMSCodeServiceImpl implements SMSCodeService {

    @Autowired
    private CodeUtils codeUtils;

    @Autowired
    private MemcachedClient memcachedClient;

    /*
        Xmemcached
     */
    @Override
    public String sendCodeToSMS(String tel) {
        String code = codeUtils.generator2(tel);
        try {
            // exp 超时时间。单位s
            memcachedClient.set(tel, 10, code);
        } catch (TimeoutException | InterruptedException | MemcachedException e) {
            e.printStackTrace();
        }
        return code;
    }

    @Override
    public boolean checkCode(SMSCode smsCode) {
        String code = null;
        try {
            code = memcachedClient.get(smsCode.getTel());
        } catch (Exception e) {
            e.printStackTrace();
        }

        return smsCode.getCode().equals(code);
    }
}
```

实体类

```java
@Data
public class SMSCode {

    private String tel;
    private String code;

}
```

#### Redis

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

配置

```yml
#  cache:
#    type: redis
#    redis:
#      use-key-prefix: true
#      cache-null-values: false
#      key-prefix: aa
#      time-to-live: 10s
#  redis:
#    host: localhost
#    port: 6379
```

具体实现同上

#### Ehcache

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>net.sf.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```

配置

```yml
#ehcache配置
#  cache:
#    type: ehcache
#    ehcache:
#      config: ehcache.xml
```

ehcache.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
         updateCheck="false">
    <diskStore path="D:\ehcache" />

    <!--默认缓存策略 -->
    <!-- external：是否永久存在，设置为true则不会被清除，此时与timeout冲突，通常设置为false-->
    <!-- diskPersistent：是否启用磁盘持久化-->
    <!-- maxElementsInMemory：最大缓存数量-->
    <!-- overflowToDisk：超过最大缓存数量是否持久化到磁盘-->
    <!-- timeToIdleSeconds：最大不活动间隔，设置过长缓存容易溢出，设置过短无效果，可用于记录时效性数据，例如验证码-->
    <!-- timeToLiveSeconds：最大存活时间-->
    <!-- memoryStoreEvictionPolicy：缓存清除策略-->
    <defaultCache
        eternal="false"
        diskPersistent="false"
        maxElementsInMemory="1000"
        overflowToDisk="false"
        timeToIdleSeconds="10"
        timeToLiveSeconds="10"
        memoryStoreEvictionPolicy="LRU" />

    <cache
        name="smsCode"
        eternal="false"
        diskPersistent="false"
        maxElementsInMemory="1000"
        overflowToDisk="false"
        timeToIdleSeconds="10"
        timeToLiveSeconds="10"
        memoryStoreEvictionPolicy="LRU" />

</ehcache>
```

具体实现同上

### 定时任务

开启定时任务

```java
@SpringBootApplication
@EnableScheduling // 开始定时任务
public class Springboot12TaskApplication {}
```

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
```

配置

```yml
spring:
  task:
    scheduling:
      thread-name-prefix: spring_ # 线程前缀
```

任务配置

```java
@Configuration
public class QuartzConfig {

    @Bean
    public JobDetail jobDetail() {
        // 绑定具体的工作
        return JobBuilder.newJob(MyQuartz.class)
                .storeDurably()
                .build();
    }

    @Bean
    public Trigger trigger() {
        // 绑定具体的工作明细
        ScheduleBuilder<? extends Trigger> schedBuilder = CronScheduleBuilder
                .cronSchedule("0/5 * * * * ?");
        return TriggerBuilder.newTrigger()
                .forJob(jobDetail())
                .withSchedule(schedBuilder)
                .build();
    }
}
```

springboot提供的定时任务功能

```java
@Component
public class MyBean {

    @Scheduled(cron = "0/1 * * * * ?")
    public void print() {
        System.out.println(Thread.currentThread().getName() + "print...");
    }

}
```

### Mail(邮件)

添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

配置

```yaml
spring:
  mail:
    username: 2475058223@qq.com
    password: awxesgjpwgikdjjc
    port: 587
    host: smtp.qq.com
```

具体实现

```java
@Service
public class SendMailServiceImpl implements SendMailService {

    @Autowired
    private JavaMailSender javaMailSender;
    private String from = "2475058223@qq.com";
    private String to = "jiangdongyu18@163.com";
    private String subject = "测试邮件";

    private String content = "<img src='https://img1.baidu.com/it/u=2192429849,3208186763&fm=253&fmt=auto&app=138&f=JPEG?w=701&h=500'><a href=\"https://www.itcast.cn\">点开有惊喜</a>";

    @Override
    public void sendMail() {
       /* SimpleMailMessage message = new SimpleMailMessage();
        message.setFrom(from + "(小甜甜)");
        message.setTo(to);
        message.setSubject(subject);
        message.setText(content);*/
        try {
            MimeMessage message = javaMailSender.createMimeMessage();
            MimeMessageHelper mimeMessageHelper = new MimeMessageHelper(message, true);
            mimeMessageHelper.setFrom(from + "(小甜甜)");
            mimeMessageHelper.setTo(to);
            mimeMessageHelper.setSubject(subject);
            mimeMessageHelper.setText(content, true);
            // 如何添加附件
            File file = new File("D:\\jdy2002\\Idea\\SpringBoot2-Plus\\springboot-13-mail\\target\\springboot-13-mail-0.0.1-SNAPSHOT.jar");
            mimeMessageHelper.addAttachment(file.getName(), file);
            javaMailSender.send(message);
        } catch (MessagingException e) {
            e.printStackTrace();
        }

    }
}
```

### Admin

#### server

添加依赖

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-server</artifactId>
    <version>3.2.1</version>
</dependency>
```

开启服务

```java
@EnableAdminServer
public class SpringbootWebQuickstartApplication {}
```

配置项

```java
@Component
public class HealthConfig extends AbstractHealthIndicator {


    // 显示运行时间
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        builder.withDetail("runTime", System.currentTimeMillis())
                .up();
    }
}
```

```java
@Component
@Endpoint(id = "pay")
public class PayEndpoint {

    @ReadOperation
    public Object getPay() {
        System.out.println("===================");
        return "123";
    }

}
```

#### 客户端

添加依赖

```xml
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-starter-client</artifactId>
    <version>3.2.2</version>
</dependency>
```

配置

```yaml
server:
  port: 80

spring:
  boot:
    admin:
      client:
        url: http://localhost:8080 # 客户端地址
management:
  endpoint:
    health:
      show-details: always
    info:
      enabled: true
  endpoints:
    web:
      exposure:
        include: '*'
    enabled-by-default: true
info:
  author: itheima
  appName: @project.artifactId@
```

访问http://localhhost:8080即可
