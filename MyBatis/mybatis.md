# MyBatis #
---
# 配置 #
以使用Dynamic Web Project为例，数据库使用MySQL
* 将下载好的mybatis-x.x.x.jar和mysql-connector-java-x.x.x-bin.jar放入WebRoot下的WEB-INF中的lib文件夹下；
* 创建两个源文件夹，如src/mybatis和src/test；
* 在src/mybatis下创建configuration.xml文件，配置MyBatis：
	<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
		<typeAliases> 
			<typeAlias alias="Person" type="com.mybatis.model.Person"/> 
		</typeAliases> 
		<environments default="development">
			<environment id="development">
			<transactionManager type="JDBC"/>
 				<dataSource type="POOLED">
					<property name="driver" value="com.mysql.jdbc.Driver"/>
					<property name="url" value="jdbc:mysql://127.0.0.1:3306/test" />
					<property name="username" value="root"/>
					<property name="password" value="root"/>
				</dataSource>
			</environment>
		</environments>
    		<mappers>
			<mapper resource="com/mybatis/model/Person-mapping.xml"/>
		</mappers>
	</configuration>

# ResultMap #

# CRUD #

# 动态SQL #
