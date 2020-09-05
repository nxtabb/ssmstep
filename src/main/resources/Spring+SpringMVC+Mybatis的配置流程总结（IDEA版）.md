# Spring+SpringMVC+Mybatis的配置流程总结（IDEA版）

## 一、创建一个maven工程

## 1、使用maven-archetypes:maven-archetypes-webapp骨架

## 二、构建项目结构

### 1、建立项目结构目录 

### 一般如下：

![image-20200905200328476](C:\Users\Administrator\Desktop\Spring+SpringMVC+Mybatis的配置流程总结（IDEA版）.assets\image-20200905200328476.png)

```
其中：main/java存放各种类、接口，典型的如：controller、service、dao、Pojo、utils等
```

```
其中：main/resources下存放各种配置文件，例如：spring配置文件，数据库连接信息，mybatis配置文件，springmvc配置文件，mybatis实现的xml文件
```

```
其中：webapp/WEB-INF下存放不想让外界直接访问的数据，例如html页面
```

```
其中：webapp/resources下存放各种静态资源，后面springmvc会专门配置如何访问静态资源
```

## 2、设计数据库，功能、表、实体，建立Pojo类，创建数据库表

## 3、使用pom.xml引入依赖

#### spring+springmvc+mybatis的依赖一般如下：

```xml
	<dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.1.4.RELEASE</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-test -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.1.4.RELEASE</version>
      <scope>test</scope>
    </dependency>
    <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>org.mybatis.generator</groupId>
      <artifactId>mybatis-generator-maven-plugin</artifactId>
      <version>1.3.5</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.2</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.1</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.37</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/c3p0/c3p0 -->
    <dependency>
      <groupId>c3p0</groupId>
      <artifactId>c3p0</artifactId>
      <version>0.9.1.2</version>
    </dependency>
	<dependency>
      <groupId>commons-collections</groupId>
      <artifactId>commons-collections</artifactId>
      <version>3.2</version>
    </dependency>
```

### 4、数据库连接配置

#### 1.在main/resources下面创建db.properties 表示与数据库的连接信息

```properties
jdbc.url = jdbc:mysql://localhost:3306/o2o?useUnicode=true&characterEncoding=utf8&serverTimezone=CST
jdbc.driver = com.mysql.jdbc.Driver
jdbc.user = root
jdbc.password = root
```

#### 2.在main/resources创建mybatis.xml文件配置mybatis

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <!--获取数据库自增主键值-->
        <setting name="useGeneratedKeys" value="true"/>
        <!--使用列标签替换列名-->
        <setting name="useColumnLabel" value="true"/>
        <!--开启驼峰命名转化-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
</configuration>

```

#### 3.在main/resources下创建spring文件夹，代表配置spring的相关信息

##### 1.创建spring-dao.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--jdbc的属性-->
    <context:property-placeholder location="classpath:db.properties"/>
    <!--数据库连接池的属性-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.url}"/>
        <property name="driverClass" value="${jdbc.driver}"/>
        <property name="user" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
        <!--数据库连接池私有属性-->
        <property name="maxPoolSize" value="30"/>
        <property name="minPoolSize" value="5"/>
        <property name="acquireIncrement" value="5"/>
        <property name="initialPoolSize" value="20"/>
        <property name="autoCommitOnClose" value="false"/>
        <property name="acquireRetryAttempts" value="2"/>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:mybatis.xml"/>
        <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>

    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <property name="basePackage" value="com.hrbeu.O2O.dao"/>
    </bean>


</beans>
```

##### 2.创建spring-service.xml 配置spring的事务管理等

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd
       http://www.springframework.org/schema/tx
       http://www.springframework.org/schema/tx/spring-tx.xsd">

    <context:component-scan base-package="com.hrbeu.O2O.service"/>

    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <tx:annotation-driven transaction-manager="transactionManager"/>
</beans>
```

##### 3.创建spring-controller 即配置spring-mvc的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
		http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
		http://www.springframework.org/schema/tx
		http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	  http://www.springframework.org/schema/aop
	  http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
    <!--自动扫描controller下所有的类，然后就可以使用注解了-->
    <context:component-scan base-package="com.hrbeu.O2O.controller"/>
    <!--开启springMVC的注解模式-->
    <mvc:annotation-driven/>
    <!--静态资源默认servlet配置-->
    <!--可以使用 / 做整体的映射-->
    <mvc:resources mapping="/resources/**" location="/resources/"/>
    <!--交由默认的servlet处理-->
    <mvc:default-servlet-handler/>
    <!--视图解析器-->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/html/"/>
        <property name="suffix" value=".html"/>
    </bean>
    <!--文件上传解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <property name="defaultEncoding" value="utf-8"/>
        <!--上传文件大小限制20M-->
        <property name="maxUploadSize" value="20971520"/>
        <!--上传文件内存-->
        <property name="maxInMemorySize" value="20971520"/>
    </bean>
</beans>
```

##### 4.配置web.xml进行整合

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">
<web-app>
  <servlet>
    <servlet-name>spring-dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:spring/spring-*.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  <servlet-mapping>
    <servlet-name>spring-dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>

```

