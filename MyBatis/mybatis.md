# MyBatis #
---
# 配置 #
以使用Dynamic Web Project为例，数据库使用MySQL
* 将下载好的mybatis-x.x.x.jar和mysql-connector-java-x.x.x-bin.jar放入WebRoot下的WEB-INF中的lib文件夹下；
* 创建两个源文件夹，如src/mybatis和src/test；
* 在src/mybatis下创建configuration.xml文件，配置MyBatis：
```xml
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
```
    一个完整的配置文件有如下的目录结构（即configuration子标签）：  
configuration `//父标签`  
|--- properties `//配置属性`  
|--- settings `//设置`  
|--- typeAliases `//类型别名`  
|--- typeHandlers `//类型句柄`  
|--- objectFactory `//对象工厂`  
|--- plugins `//插件`  
|--- environments `//环境，可以设置多个环境`  
|--- |__ environment `//单个环境的配置`  
|　　　　|--- transactionManager `//事物管理器，一般为JDBC`  
|　　　　|__ dataSource `//数据源`  
|　　　　　　|__ property `//数据源属性：数据库驱动、数据库url、数据库用户名密码等`  
|__ mappers `//映射器`

* 在src/mybatis下创建com.mybatis.model包，创建数据库表格对应实体类，如Person：
```java
package com.mybatis.model;

public class Person 
{
	private String id;
	private String name;
	private int age;
	
	public Person(){}
	
	public Person(String id,String name,int age)
	{
		this.id = id;
		this.name = name;
		this.age = age;
	}
	public String getId() 
	{
		return id;
	}
	
	public void setId(String id) 
	{
		this.id = id;
	}
	
	public String getName() 
	{
		return name;
	}
	
	public void setName(String name)
	{
		this.name = name;
	}
	
	public int getAge()
	{
		return age;
	}
	
	public void setAge(int age) 
	{
		this.age = age;
	}
}
```
* 在com.mybatis.model包下创建Person-mapping.xml文件：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.mybatis.mapper.PersonMapper">
	<select id="selectAllPeople" resultType="Person">
		select * from `person`
	</select>
</mapper>
```

* 在src/mybatis下创建com.mybatis.mapper包，创建PersonMapper.java文件，写Person映射的数据库接口：
```java
package com.mybatis.mapper;

import java.util.ArrayList;

import com.mybatis.model.Person;

public interface PersonMapper 
{
	//select * from `person`
	public ArrayList<Person> selectAllPeople();
}
```

* 在src/test下创建com.mybatis.test包，创建Test.java进行测试：
```java
package com.mybatis.test;

import java.io.Reader;
import java.util.ArrayList;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.mybatis.mapper.PersonMapper;
import com.mybatis.model.Person;

public class Test 
{
    private static SqlSessionFactory sqlSessionFactory;
    private static Reader reader; 

    static
    {
        try
        {
            reader = Resources.getResourceAsReader("configuration.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        }
        catch(Exception e)
        {
            e.printStackTrace();
        }
    }

    public static SqlSessionFactory getSession()
    {
        return sqlSessionFactory;
    }
    
    public static void main(String[] args) 
    {
        SqlSession session = sqlSessionFactory.openSession();
        try 
        {
        	PersonMapper mapper = session.getMapper(PersonMapper.class);
        	ArrayList<Person> arr = mapper.selectAllPeople();
        	for(int i=0;i<arr.size();i++)
        	{
        		System.out.print((i+1)+": "+arr.get(i).getId()+" ");
        		System.out.print(arr.get(i).getName()+" ");
        		System.out.println(arr.get(i).getAge()+" ");
        	}
        	
        } 
        finally 
        {
        	session.commit();
        	session.close();
        }
    }
}
```
    正常运行即配置成功。
* 一般添加查询方法的步骤（已有相应Mapper.java和Mapping.xml文件）：
	1. 在Mapper.java（此例中是Person.java）添加相应接口，如selectXXX();
	2. 在Mapping.xml（此例中是Person-mapping.xml）添加查询标签（<select></select>等)，标签id为selectXXX(), 写入SQL语句，对应接口方法；
	3. 在测试类中测试。
* 一般添加查询方法的步骤（无相应Mapper.java和Mapping.xml文件）：
	1. 添加javaBean实体类，如foo，来对应数据库表格（已有可忽略）；
	2. 添加fooMapper.java，写入fooMapper接口，添加查询接口（如selectFoo();）；
	3. 添加foo-Mapping.xml，<mapper>标签中namespace属性为fooMapper的路径（com.mybatis.model.fooMapper）,
	    添加查询标签（<select id="selectFoo">，写入SQL语句；
	4. 在configuration.xml中添加配置信息：<typeAlias alias="foo" type="com.mybatis.model.foo">增加类型别名；
	     <mapper resource="com/mybatis/model/foo-mapping.xml" />添加映射器；
	5. 添加测试类测试。
---
# ResultMap #


# CRUD #

# 动态SQL #
