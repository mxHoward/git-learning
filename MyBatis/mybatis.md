# MyBatis #
---
## 配置 ##
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
__正常运行即配置成功。

* 一般添加查询方法的步骤（已有相应Mapper.java和Mapping.xml文件）：
	1. 在Mapper.java（此例中是Person.java）添加相应接口，如selectXXX();
	2. 在Mapping.xml（此例中是Person-mapping.xml）添加查询标签（`<select></select>`等)，标签id为selectXXX(), 写入SQL语句，对应接口方法；
	3. 在测试类中测试。

* 一般添加查询方法的步骤（无相应Mapper.java和Mapping.xml文件）：
	1. 添加javaBean实体类，如foo，来对应数据库表格（已有可忽略）；
	2. 添加fooMapper.java，写入fooMapper接口，添加查询接口（如selectFoo();）；
	3. 添加foo-Mapping.xml，<mapper>标签中namespace属性为fooMapper的路径（com.mybatis.model.fooMapper），添加查询标签（`<select id="selectFoo"`>，写入SQL语句；
	4. 在configuration.xml中添加配置信息：`<typeAlias alias="foo" type="com.mybatis.model.foo">`增加类型别名；`<mapper resource="com/mybatis/model/foo-mapping.xml" />`添加映射器；
	5. 添加测试类测试。

---

## ResultMap ##
对于一个作为领域模型实体类的javaBean，比如下面的java代码：
```java
package com.someapp.model;
public class User 
{
 	private int id;
 	private String username;
 	private String hashedPassword;
  
 	public int getId() 
 	{
		return id;
	}
 	public void setId(int id) {
		this.id = id;
 	}
 	public String getUsername()
 	{
		return username;
	}
	public void setUsername(String username) 
	{
		this.username = username;
	}
	public String getHashedPassword()
	{
		return hashedPassword;
	}
	public void setHashedPassword(String hashedPassword)
	{
		this.hashedPassword = hashedPassword;
	}
}
```
这样一个JavaBean可以被映射到结果集，用来匹配数据库列名和实体类属性名：
```xml
<resultMap id="userResultMap" type="User">
	<id property="id" column="user_id" />
	<result property="username" column="user_name"/>
	<result property="password" column="hashed_password"/>
</resultMap>
```
* ResultMap概念视图（子标签）
	+ consturctor 类在实例化时,用来注入结果到构造方法中
		+ idArg - ID 参数;标记结果作为 ID 可以帮助提高整体效能
		+ arg - 注入到构造方法的一个普通结果
	+ id – 一个 ID 结果;标记结果作为 ID 可以帮助提高整体效能
	+ result – 注入到字段或 JavaBean 属性的普通结果
	+ association – 一个复杂的类型关联;许多结果将包成这种类型
		+ 嵌入结果映射 – 结果映射自身的关联,或者参考一个
	+ collection – 复杂类型的集
		+ 嵌入结果映射 – 结果映射自身的集,或者参考一个
	+ discriminator – 使用结果值来决定使用哪个结果映射
		+ case – 基于某些值的结果映射
			+ 嵌入结果映射 – 这种情形结果也映射它本身,因此可以包含很多相 同的元素,或者它可以参照一个外部的结果映射。

* ResultMap Attributes
* id&result
* consturctor
* association
* collection
* discriminator

## CRUD ##
---
### R--select ###
* select example
```xml
<select id="selectPerson" parameterType="int" resultType="Person">
	SELECT * FROM PERSON WHERE ID = #{id}
</select>
```
* select attributes
```xml
<select
	id="selectPerson"  <!--在命名空间中唯一的标识符，可以被用来引用这条语句。-->
	parameterType="int"  <!--将会传入这条语句的参数类的完全限定名或别名，可选的-->
	resultType="Person"  <!--从这条语句中返回的期望类型的类的完全限定名或别名。-->
	resultMap="personResultMap"  <!--外部 resultMap 的命名引用。使用 resultMap 或 resultType，但不能同时使用。-->
	flushCache="false"  <!--将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：false。-->
	useCache="true"  <!--将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true。-->
	timeout="10000"  <!--这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。-->
	fetchSize="256"  <!--这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为 unset（依赖驱动）。-->
	statementType="PREPARED"  <!--STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。-->
	resultSetType="FORWARD_ONLY"  <!--FORWARD_ONLY，SCROLL_SENSITIVE 或 SCROLL_INSENSITIVE 中的一个，默认值为 unset （依赖驱动）。-->
>
```
### C--insert ###
* insert example
```xml
<insert id="insertAuthor">
	insert into Author (id,username,password,email,bio)
	values (#{id},#{username},#{password},#{email},#{bio})
</insert>
```
*insert attributes
```xml
<insert
	id="insertAuthor"  <!--命名空间中的唯一标识符，可被用来代表这条语句。-->
	parameterType="domain.blog.Author"  <!--将要传入语句的参数的完全限定类名或别名，可选的。-->
	flushCache="true"  <!--将其设置为true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。-->
	statementType="PREPARED"  <!--STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。-->
	keyProperty=""  <!--唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认：unset。-->
	keyColumn=""  <!--通过生成的键值设置表中的列名。-->
	useGeneratedKeys=""  <!--这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键，默认为false-->
	timeout="20"  <!--这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。-->
>
```
### U--update ###
* update example
```xml
<update id="updateAuthor">
	update Author set
	username = #{username},
	password = #{password},
	email = #{email},
	bio = #{bio}
	where id = #{id}
</update>
```
* update attributes
```xml
<update
	id="updateAuthor"  <!--命名空间中的唯一标识符，可被用来代表这条语句。-->
	parameterType="domain.blog.Author"  <!--将要传入语句的参数的完全限定类名或别名，可选的。-->
	flushCache="true"  <!--将其设置为true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。-->
	statementType="PREPARED"  <!--STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。-->
	useGeneratedKeys=""  <!--这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键，默认为false-->
	timeout="20"  <!--这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。-->
>
```
### D--delete ###
* delete example
```xml
<delete id="deleteAuthor">
	delete from Author where id = #{id}
</delete>
```
* delete attributes
```xml
<delete
	id="deleteAuthor"  <!--命名空间中的唯一标识符，可被用来代表这条语句。-->
	parameterType="String"  <!--将要传入语句的参数的完全限定类名或别名，可选的。-->
	flushCache="true"  <!--将其设置为true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。-->
	statementType="PREPARED"  <!--STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。-->
	timeout="20"  <!--这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。-->
>
```
## 动态SQL ##
* sql
* if
* choose, when, otherwise
* where
* set
* trim
* foreach
* bind

