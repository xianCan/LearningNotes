# MyBatis学习笔记

## 第一章  发展历程

**JDBC ==》Dbutils（QueryRunner） ==》JdbcTemplate ==》 Hibernate ==》Mybatis、String Data JPA**

## 第二章  HelloWorld

### 2.1  入门级案例

* 在maven中引入springboot的单元测试相关包

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-jdbc</artifactId>
  </dependency>
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <scope>runtime</scope>
  </dependency>
  <!--单元测试-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
  </dependency>
  
  ```

* 引入xml的配置文件（全局配置文件）创建一个SqlSessionFactory对象

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC" />
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="root"/>
              </dataSource>
          </environment>
      </environments>
  
      <mappers>
          <mapper resource="mybatis/mapper/EmployeeMapper.xml" />
      </mappers>
  </configuration>
  ```

* sql映射文件，配置了每一个sql，以及sql的封装规则

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper namespace="com.xianCan.springboot.mapper.EmployeeMapper">
  
      <select id="getEmpById" parameterType="java.lang.Integer" resultType="com.xianCan.springboot.bean.Employee">
          select id,last_name lastName,gender,email,dId from tb_employee where id = #{id}
      </select>
  
  </mapper>
  ```

* 将sql映射文件注册到全局配置文件中

* 测试代码

  ```java
  package com.xianCan.springboot.test;
  
  import com.xianCan.springboot.bean.Employee;
  import org.apache.ibatis.io.Resources;
  import org.apache.ibatis.session.SqlSession;
  import org.apache.ibatis.session.SqlSessionFactory;
  import org.apache.ibatis.session.SqlSessionFactoryBuilder;
  import org.junit.Test;
  import org.springframework.boot.test.context.SpringBootTest;
  
  import java.io.IOException;
  import java.io.InputStream;
  
  /**
   * @author xianCan
   * @date 2020/3/14 12:05
   */
  @SpringBootTest
  public class MybatisTest {
  
      @Test
      public void test() throws IOException {
          String resource = "mybatis/mybatis-config.xml";
          InputStream resourceAsStream = Resources.getResourceAsStream(resource);
          SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
          SqlSession sqlSession = sessionFactory.openSession();
  
          Employee employee = sqlSession.selectOne("getEmpById", 1);
          System.out.println(employee);
      }
  }
  
  ```

### 2.2  接口式编程

* 一个mapper和一个xml文件对应，xml文件中namespace填入对应的mapper全路径

  ```java
  EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
  Employee employee = mapper.getEmpById(1);
  System.out.println(employee);
  ```

* 接口式编程

  原生	Dao	===》	DaoImpl

  myBatis	Mapper	===》	xxxMapper.xml

* SqlSession代表和数据库的一次会话，用完必须关闭

* SqlSession和connection一样都是非线程安全的，不能放在成员变量  

* mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象

* 两个重要的配置文件

  * myBatis的全局配置文件（文件不一定有，有可能直接写在yml配置文件或者直接用配置类替换）：包  

    含数据库连接池信息，事务管理器信息，系统运行环境信息等

  * sql映射文件，保存了每一个sql语句的映射信息

## 第三章   全局配置文件

### 2.1  properties

* **mybatis可以使用使用properties来引入外部properties配置文件的内容**

* **properties**：引入外部资源

  * **resource**：引入类路径下的资源

  * **url**：引入网络路径或者磁盘路径下的资源

* 在mybatis路径下新建外部配置文件dbconfig.properties

  ```
  jdbc.driver=com.mysql.cj.jdbc.Driver
  jdbc.url=jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8
  jdbc.username=root
  jdbc.password=root
  ```

* 修改mybatis-config.xml文件，引入properties标签，将数据库链接的value值换成通配符取值

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuration>
  
      <!--引入类路径下的配置资源-->
      <properties resource="mybatis/dbconfig.properties" />
  
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC" />
              <dataSource type="POOLED">
                  <!--value改为动态获取-->
                  <property name="driver" value="${jdbc.driver}"/>
                  <property name="url" value="${jdbc.url}"/>
                  <property name="username" value="${jdbc.username}"/>
                  <property name="password" value="${jdbc.password}"/>
              </dataSource>
          </environment>
      </environments>
  
      <mappers>
          <mapper resource="mybatis/mapper/EmployeeMapper.xml" />
      </mappers>
  
  </configuration>
  ```

### 2.2  settings

* **用于设置一些mybatis的运行时行为**

* **settings**：设置配置项

  * **name**：设置项名（可取官网查询具体名称）
  * **value**：设置值

* 在mybatis的全局配置文件mybatis-config.xml中开启驼峰命名与下划线互转（举例）

  ```xml
  <settings>
      <setting name="mapUnderscoreToCamelCase" value="true" />
  </settings>
  ```

### 2.3  typeAliases

* **别名处理器，可以为java类型起别名**

* **typeAliases**：为类起别名，别名不区分大小

  * **typeAlias**：为某个java类起别名

    * **type**：指定要起别名的类全类名
    * **name**：指定新的别名，不填写该项默认类名小写

  * **package**：为某个包下的所有类批量起别名

    * **name**：指定包名（为当前包以及下面所有子包的每一个类都起一个类名小写的别名）

    * 注意：使用package时如果包下及子包出现同名类，运行时会产生冲突报错，可以使用@Alias注解  

      在类上指定别名

* 为Employee类起别名（建议resultType还是全类名，全类名容易找到返回值的数据结构）

  ```xml
  <typeAliases>
  	<typeAlias type="com.xianCan.springboot.bean.Employee" alias="employee" />
  </typeAliases>
  ```

### 2.4  typeHandler

* **将java类型和数据库类型进行匹配**

### 2.5  plugins

* **可以使用插件对mybatis运行时的一些核心步骤进行拦截，如下为mybatis四大对象**
  * Executor(update,query,flushStatements,commit,rollback,getTransaction.close,isClosed)
  * ParameterHandler(getParameterObject,setParameters)
  * ResultSetHandler(handleResultSets,handleOutputParameters)
  * StatementHandler(prepare,parameterize,batch,update,query)


### 2.6  environments

* **可以配置多种mybatis环境变量，default指定使用某种环境，可以达到快速切换环境的目的**

* **environments**：有一个id属性，可以用于指定当前环境id

  * **transactionManager**：事务管理器

    * **type**：事务管理器的类型， 

      JDBC(JdbcTransactionFactory)|MANAGED(ManagerTransactionFactory)，也可以自定义事务管理  

      器：实现TransactionFactory接口，type指定为全类名

  * **dataSource**：数据源

    * **type**：数据源类型，UNPOOLED(UnpooledDataSourceFactory)|  

      |POOLED(PooledDataSourceFactory)（连接池技术）|JNDI(JndiDataSourceFactory)  

      自定义数据源：实现DataSourceFactory接口，type指定为全类名

### 2.7  Mappers

* **将sql映射注册到全局配置中**
* **mapper**：注册一个sql映射
  * **resource**：引用类路径下的sql文件
  * **url**：引用网络或磁盘路径下的sql映射文件
  * **class**：引用注册接口（少用）
    * 有sql映射文件，映射文件名必须和接口同名，并且放在与接口同一目录下
    * 没有sql映射，所有sql都利用注解写在接口上

## 第三章  sql映射文件

































