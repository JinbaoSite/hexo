---
title: 基于Spring boot的Spring data jpa连接MySQL数据库
date: 2017-10-15 11:27:10
categories: [Spring boot]
---

<!--more-->
## 1 创建项目

以创建一个员工信息管理项目为例，创建一个Maven工程（需要提前配置好Maven环境）。
打开File->New->Other->Maven->Maven Project
![](http://7xwian.com1.z0.glb.clouddn.com/20170825213026.jpg)
Next
![](http://7xwian.com1.z0.glb.clouddn.com/20170825213203.jpg)
Next
![](http://7xwian.com1.z0.glb.clouddn.com/20170825213426.jpg)
Finish，一个Maven工程就建好了。

## 2 配置pom.xml

配置pom.xml，pom.xml主要用来描述项目：配置文件、开发者需要遵循的规则、缺陷管理系统、组织和licenses、项目的url、项目的依赖性以及其他所有的项目相关因素。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.jinbaosite</groupId>
	<artifactId>EmployeeManager</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<!-- 版本控制 -->
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.6.RELEASE</version>
	</parent>
	<dependencies>
		<!-- spring-boot-starter-web -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!-- spring-boot-starter-data-jpa -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<!-- mysql-connector-java -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
		<!-- spring-boot-configuration-processor -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<optional>true</optional>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```
保持后，右击项目->Maven->Update project，等待下载好项目所需要的包，点开项目可以发现多了一个Maven Dependencies
![](http://7xwian.com1.z0.glb.clouddn.com/20170825214921.jpg)

## 2 配置application.properties

在src/main/resource下新建一个application.properties文件（点击文件夹src/main/resource，然后New->File）
application.properties文件主要用来配置MySQL的连接
![](http://7xwian.com1.z0.glb.clouddn.com/20170825215439.jpg)
```
# 服务器访问端口
server.port=8080
# 数据库基本配置
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

spring.jpa.database=MYSQL
# 显示后台处理的SQL语句
spring.jpa.show-sql=true
# 自动检查实体和数据库表是否一致，如果不一致则会进行更新数据库表
spring.jpa.hibernate.ddl-auto=update
```

## 3 配置Application.java
Application.java是作为Spring boot的启动配置，当需要运行这个项目时，Run这个Application.java即可
创建com.jinbaosite.employeemanager包，然后在包下新建Application.java，代码如下(代码不含包，请自行导入)
```
@EnableAutoConfiguration
@EnableTransactionManagement
@ServletComponentScan
@ComponentScan
@EnableScheduling
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class,args);
	}
}
```

## 4 编写实体层Employee.java
创建com.jinbaosite.employeemanager.domain包，然后在包下新建Employee.java，代码如下（自行导入包）
```
@Entity
@Table(name="employee")
public class Employee {
	private static final long serialVersionUID = 1L;
	
	@Id
	@Column(name="id")
	private String id;
	@Column(name="name")
	private String name;
	@Column(name="gender")
	private String gender;
	@Column(name="age")
	private int age;
}
```
@Entity代表这是一个实体类，@Table(name="employee")用来对应MySQL中的employee数据库，@Id用来表达主键，@Column(name="id")表明一个id属性。
这里还需要提供get/set方法，在空白处右击鼠标->Source->Generate Getters and Setters，选中所有的成员，然后OK就可以增加get/set方法了。

## 5 编写dao层
dao层主要用来实现对数据库的增、删、查、改。
创建com.jinbaosite.employeemanager.dao包，然后在包下新建接口EmployeeRepository.java，代码如下（自行导入包）
```
public interface EmployeeRepository extends JpaRepository<Employee,String> {

}
```
接口JpaRepository来自Spring data jpa，后面是实体类以及实体类在数据库中的主键属性。

## 6 编写controller层
controller层主要用来与前端交互的
创建com.jinbaosite.employeemanager.controller包，然后在包下新建接口EmployeeController.java，代码如下（自行导入包）
```
@RestController
@RequestMapping("/employee")
public class EmployeeController {
	@RequestMapping("queryAll")
	@ResponseBody
	public List<Employee> queryAll(){
		List<Employee> list = new ArrayList<Employee>();
		list = employeeRepository.findAll();
		return list;
	}
	@Autowired
	private EmployeeRepository employeeRepository;
}
```

## 7 验证连接

在MySQL中创建test数据库，然后创建employee表，在表中插入一部分数据，通过浏览器访问http://localhost:8080/employee/queryAll，结果如下说明访问成功。
![](http://7xwian.com1.z0.glb.clouddn.com/20170825224137.jpg)