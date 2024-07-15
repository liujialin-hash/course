## 1.逆向工程

1. 在项目中配置resources/generatorConfig.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE generatorConfiguration
           PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
           "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
   <generatorConfiguration>
       <!--
       targetRuntime: 执行生成的逆向工程的版本
       MyBatis3Simple: 生成基本的CRUD（清新简洁版）
       MyBatis3: 生成带条件的CRUD（奢华尊享版）
       -->
       <context id="DB2Tables" targetRuntime="MyBatis3Simple">
           <!-- 数据库的连接信息 -->
           <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                           connectionURL="jdbc:mysql://localhost:3306/icms?characterEncoding=UTF8"
                           userId="root"
                           password="root">
           </jdbcConnection>
           <!-- javaBean的生成策略-->
           <javaModelGenerator targetPackage="com.brcb.entity"
                               targetProject=".\src\main\java">
               <property name="enableSubPackages" value="true"/>
               <property name="trimStrings" value="true"/>
           </javaModelGenerator>
           <!-- SQL映射文件的生成策略 -->
           <sqlMapGenerator targetPackage="mapper"
                            targetProject=".\src\main\resources">
               <property name="enableSubPackages" value="true"/>
           </sqlMapGenerator>
           <!-- Mapper接口的生成策略 -->
           <javaClientGenerator type="XMLMAPPER"
                                targetPackage="com.mapper" targetProject=".\src\main\java">
               <property name="enableSubPackages" value="true"/>
           </javaClientGenerator>
           <!-- 逆向分析的表 -->
           <!-- tableName设置为*号，可以对应所有表，此时不写domainObjectName -->
           <!-- domainObjectName属性指定生成出来的实体类的类名 -->
           <!--        <table tableName="news" domainObjectName="news"/>-->
           <!--        <table tableName="news_type" domainObjectName="newsType"/>-->
           <!--        <table tableName="user_info" domainObjectName="userInfo"/>-->
           <table tableName="*"/>
       </context>
   </generatorConfiguration>
   ```

2. 配置pom.xml

   ```xml
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <groupId>org.example</groupId>
       <artifactId>MyTest</artifactId>
       <packaging>war</packaging>
       <version>1.0-SNAPSHOT</version>
       <name>MyTest Maven Webapp</name>
       <url>https://maven.apache.org</url>
       <properties>
           <log4j.version>3.0.0-beta2</log4j.version>
           <tomcat.version>11.0.0-M20</tomcat.version>
       </properties>
       <dependencies>
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>3.8.1</version>
               <scope>test</scope>
           </dependency>
   
           <!--log4j2的核心-->
           <dependency>
               <groupId>org.apache.logging.log4j</groupId>
               <artifactId>log4j-core</artifactId>
               <version>${log4j.version}</version>
           </dependency>
   
           <!--log4j2的api-->
           <dependency>
               <groupId>org.apache.logging.log4j</groupId>
               <artifactId>log4j-api</artifactId>
               <version>${log4j.version}</version>
           </dependency>
   
           <dependency>
               <groupId>org.apache.logging.log4j</groupId>
               <artifactId>log4j-slf4j-impl</artifactId>
               <version>${log4j.version}</version>
           </dependency>
   
           <dependency>
               <groupId>org.apache.logging.log4j</groupId>
               <artifactId>log4j-web</artifactId>
               <version>2.17.0</version>
           </dependency>
           <!--log4j2end-->
   
           <dependency>
               <groupId>org.apache.tomcat.embed</groupId>
               <artifactId>tomcat-embed-core</artifactId>
               <version>${tomcat.version}</version>
           </dependency>
           <dependency>
               <groupId>org.apache.tomcat</groupId>
               <artifactId>tomcat-annotations-api</artifactId>
               <version>${tomcat.version}</version>
           </dependency>
           <dependency>
               <groupId>org.apache.tomcat.embed</groupId>
               <artifactId>tomcat-embed-logging-juli</artifactId>
               <version>8.0.48</version>
           </dependency>
           <dependency>
               <groupId>com.google.protobuf</groupId>
               <artifactId>protobuf-java</artifactId>
               <version>3.21.7</version>
           </dependency>
           <dependency>
               <groupId>mysql</groupId>
               <artifactId>mysql-connector-java</artifactId>
               <version>8.0.28</version>
           </dependency>
           <dependency>
               <groupId>org.mybatis.generator</groupId>
               <artifactId>mybatis-generator-core</artifactId>
               <version>1.4.0</version>
           </dependency>
   
           <dependency>
               <groupId>org.mybatis</groupId>
               <artifactId>mybatis</artifactId>
               <version>3.5.7</version>
           </dependency>
       </dependencies>
       <build>
           <!-- 构建过程中用到的插件 -->
           <plugins>
   
               <plugin>
                   <groupId>org.mybatis.generator</groupId>
                   <artifactId>mybatis-generator-maven-plugin</artifactId>
                   <version>1.4.0</version>
                   <executions>
                       <execution>
                           <id>Generate MyBatis Artifacts</id>
                           <goals>
                               <goal>generate</goal>
                           </goals>
                       </execution>
                   </executions>
                   <dependencies>
                       <dependency>
                           <groupId>org.mybatis.generator</groupId>
                           <artifactId>mybatis-generator-core</artifactId>
                           <version>1.4.0</version>
                       </dependency>
                       <dependency>
                           <groupId>mysql</groupId>
                           <artifactId>mysql-connector-java</artifactId>
                           <version>8.0.28</version>
                       </dependency>
                   </dependencies>
               </plugin>
           </plugins>
   
   
           <finalName>MyTest</finalName>
       </build>
   </project>
   
   ```

3. 在项目根目录下运行

   ```sh
   --如果切换目录失败
   cd /d D:\workSpace\2024\MyTest\MyTest
   mvn mybatis-generator:generate
   ```

   

