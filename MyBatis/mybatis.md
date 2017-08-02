# MyBatis #
---
## 配置 ##
以使用Dynamic Web Project为例，数据库使用MySQL
*	将下载好的mybatis-x.x.x.jar和mysql-connector-java-x.x.x-bin.jar放入WebRoot下的WEB-INF中的lib文件夹下；
*	创建两个源文件夹，如src/mybatis和src/test；
*	在src/mybatis下创建configuration.xml文件，配置MyBatis：
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

* 	在src/mybatis下创建com.mybatis.model包，创建数据库表格对应实体类，如Person：
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

*	 在com.mybatis.model包下创建Person-mapping.xml文件：
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

*	 在src/mybatis下创建com.mybatis.mapper包，创建PersonMapper.java文件，写Person映射的数据库接口：
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

* 	在src/test下创建com.mybatis.test包，创建Test.java进行测试：
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
	__正常运行即配置成功。__

*	一般添加查询方法的步骤（已有相应Mapper.java和Mapping.xml文件）：
	1.	在Mapper.java（此例中是Person.java）添加相应接口，如selectXXX();
	2.	在Mapping.xml（此例中是Person-mapping.xml）添加查询标签（`<select></select>`等)，标签id为selectXXX(), 写入SQL语句，对应接口方法；
	3.	在测试类中测试。

*	一般添加查询方法的步骤（无相应Mapper.java和Mapping.xml文件）：
	1.	添加javaBean实体类，如foo，来对应数据库表格（已有可忽略）；
	2.	添加fooMapper.java，写入fooMapper接口，添加查询接口（如selectFoo();）；
	3.	添加foo-Mapping.xml，<mapper>标签中namespace属性为fooMapper的路径（com.mybatis.model.fooMapper），添加查询标签（`<select id="selectFoo"`>，写入SQL语句；
	4.	在configuration.xml中添加配置信息：`<typeAlias alias="foo" type="com.mybatis.model.foo">`增加类型别名；`<mapper resource="com/mybatis/model/foo-mapping.xml" />`添加映射器；
	5.	添加测试类测试。

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
*	ResultMap概念视图（子标签）
	+ consturctor - 类在实例化时,用来注入结果到构造方法中
		+ idArg - ID 参数;标记结果作为 ID可以帮助提高整体效能
		+ arg - 注入到构造方法的一个普通结果
	+ id – 一个 ID 结果;标记结果作为ID可以帮助提高整体效能
	+ result – 注入到字段或JavaBean属性的普通结果
	+ association – 一个复杂的类型关联；许多结果将包成这种类型
		+ 嵌入结果映射 – 结果映射自身的关联，或者参考一个
	+ collection – 复杂类型的集合
		+ 嵌入结果映射 – 结果映射自身的集,或者参考一个
	+ discriminator – 使用结果值来决定使用哪个结果映射
		+ case – 基于某些值的结果映射
			+ 嵌入结果映射 – 这种情形结果也映射它本身,因此可以包含很多相 同的元素,或者它可以参照一个外部的结果映射。

*	ResultMap Attributes
	+ id - 当前命名空间中的一个唯一标识，用于标识一个result map.
	+ type - 类的全限定名, 或者一个类型别名。
	+ autoMapping - 如果设置这个属性，MyBatis将会为这个ResultMap开启或者关闭自动映射。这个属性会覆盖全局的属性autoMappingBehavior。默认值为：unset。

*	id&result - ResultMap中最基本的内容

	```xml
	<id property="id" column="post_id"/>
	<result property="subject" column="post_subject"/>
	```
	Attributes:
	+ property - 映射到列结果的字段或属性。
	+ column - 从数据库中得到的列名,或者是列名的重命名标签。
	+ javaType - 一个Java类的完全限定名，或一个类型别名。
	+ jdbcType - 在这个表格之后的所支持的JDBC类型列表中的类型。
	+ typeHandler - 我们在前面讨论过默认的类型处理器。使用这个属性,你可以覆盖默认的类型处理器。这个属性值是类的完全限定名或者是一个类型处理 器的实现,或者是类型别名。

*	constructor -  构造方法注入允许你在初始化时为类设置属性的值,而不用暴露出公有方法。
	```java
	public class User
	{
		//...
		public User(Integer id, String username, int age)
		{
		//...
		}
		//...
	}
	```
	对于上述构造方法，可以在MyBatis中注入查询结果：
	```xml
	<constructor>
		<idArg column="id" javaType="int" name="id"/>
		<arg column="username" javaType="String" name="username“/>
		<arg column="age" javaType="_int" name="age"/>
	</constructor>
	```
	Attributes:
	+ column - 来自数据库的类名，或重命名的列标签。
	+ javaType - 一个Java类的完全限定名，或一个类型别名。
	+ jdbcType - 在这个表格之前的所支持的JDBC类型列表中的类型。
	+ typeHandler - 类型处理器。
	+ select - 用于加载复杂类型属性的映射语句的ID，从column中检索出来的数据，将作为此select语句的参数。
	+ resultMap - ResultMap的ID，可以将嵌套的结果集映射到一个合适的对象树中，功能和select属性相似，它可以实现将多表连接操作的结果映射成一个单一的ResultSet。
	+ name - Java类中的构造方法的参数。

* 	association - 关联元素处理“有一个”类型的关系。
	关联中不同的是你需要告诉 MyBatis 如何加载关联。（两种方式）
	1.	嵌套查询:通过执行另外一个 SQL 映射语句来返回预期的复杂类型。（性能通常很糟糕）  
		example:
		```xml
		<resultMap id="blogResult" type="Blog">
		<association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
		</resultMap>

		<select id="selectBlog" resultMap="blogResult">
			SELECT * FROM BLOG WHERE ID = #{id}
		</select>

		<select id="selectAuthor" resultType="Author">
			SELECT * FROM AUTHOR WHERE ID = #{id}
		</select>
		```
	2.	嵌套结果:使用嵌套结果映射来处理重复的联合结果的子集。（性能相对较好）  
		example:
		```xml
		<resultMap id="blogResult" type="Blog">
			<id property="id" column="blog_id" />
			<result property="title" column="blog_title"/>
			<association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
		</resultMap>

		<resultMap id="authorResult" type="Author">
			<id property="id" column="author_id"/>
			<result property="username" column="author_username"/>
			<result property="password" column="author_password"/>
			<result property="email" column="author_email"/>
			<result property="bio" column="author_bio"/>
		</resultMap>
		<select id="selectBlog" resultMap="blogResult">
			select
			B.id            as blog_id,
			B.title         as blog_title,
			B.author_id     as blog_author_id,
			A.id            as author_id,
			A.username      as author_username,
			A.password      as author_password,
			A.email         as author_email,
			A.bio           as author_bio
			from Blog B left outer join Author A on B.author_id = A.id
			where B.id = #{id}
		</select>
		```

*	collection - 集合元素的作用几乎和关联是相同的，表示“有很多个”的关系。  
	example:
	```xml
	<collection property="posts" ofType="domain.blog.Post">
		<id property="id" column="post_id"/>
		<result property="subject" column="post_subject"/>
		<result property="body" column="post_body"/>
	</collection>
	```
	集合也有两种类型：
	1.	集合的嵌套查询。  
		example:
		```xml
		<resultMap id="blogResult" type="Blog">
			<collection property="posts" javaType="ArrayList" column="id" ofType="Post" select="selectPostsForBlog"/>
		</resultMap>

		<select id="selectBlog" resultMap="blogResult">
			SELECT * FROM BLOG WHERE ID = #{id}
		</select>

		<select id="selectPostsForBlog" resultType="Post">
			SELECT * FROM POST WHERE BLOG_ID = #{id}
		</select>
		```
	2.	集合的嵌套结果。  
		example:
		```xml
		<resultMap id="blogResult" type="Blog">
			<id property="id" column="blog_id" />
			<result property="title" column="blog_title"/>
			<collection property="posts" ofType="Post">
				<id property="id" column="post_id"/>
				<result property="subject" column="post_subject"/>
				<result property="body" column="post_body"/>
			</collection>
		</resultMap>

		<select id="selectBlog" resultMap="blogResult">
			select
			B.id as blog_id,
			B.title as blog_title,
			B.author_id as blog_author_id,
			P.id as post_id,
			P.subject as post_subject,
			P.body as post_body,
			from Blog B
			left outer join Post P on B.id = P.blog_id
			where B.id = #{id}
		</select>
		```
*	discriminator - 处理一个单独的数据库查询返回很多不同 (但是希望有些关联) 数据类型的结果集的情况。  
	example:
	```xml
	<resultMap id="vehicleResult" type="Vehicle">
		<id property="id" column="id" />
		<result property="vin" column="vin"/>
		<result property="year" column="year"/>
		<result property="make" column="make"/>
		<result property="model" column="model"/>
		<result property="color" column="color"/>
		<discriminator javaType="int" column="vehicle_type">
			<case value="1" resultMap="carResult"/>
			<case value="2" resultMap="truckResult"/>
			<case value="3" resultMap="vanResult"/>
			<case value="4" resultMap="suvResult"/>
		</discriminator>
	</resultMap>

	<resultMap id="carResult" type="Car" extends="vehicleResult">
		<result property="doorCount" column="door_count" />
	</resultMap>

	<!--Other resultMaps-->
	```
	或者上述例子可以用另外一种简洁的语法替代：
	```xml
	<resultMap id="vehicleResult" type="Vehicle">
		<id property="id" column="id" />
		<result property="vin" column="vin"/>
		<result property="year" column="year"/>
		<result property="make" column="make"/>
		<result property="model" column="model"/>
		<result property="color" column="color"/>
		<discriminator javaType="int" column="vehicle_type">
			<case value="1" resultType="carResult">
				<result property="doorCount" column="door_count" />
			</case>
			<case value="2" resultType="truckResult">
				<result property="boxSize" column="box_size" />
				<result property="extendedCab" column="extended_cab" />
			</case>
			<case value="3" resultType="vanResult">
				<result property="powerSlidingDoor" column="power_sliding_door" />
			</case>
			<case value="4" resultType="suvResult">
				<result property="allWheelDrive" column="all_wheel_drive" />
			</case>
		</discriminator>
	</resultMap>
	```

## CRUD ##
---
### R--select ###
*	select example
	```xml
	<select id="selectPerson" parameterType="int" resultType="Person">
		SELECT * FROM PERSON WHERE ID = #{id}
	</select>
	```
*	select attributes
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
*	insert example
	```xml
	<insert id="insertAuthor">
		insert into Author (id,username,password,email,bio)
		values (#{id},#{username},#{password},#{email},#{bio})
	</insert>
	```
*	insert attributes
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
*	update example
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
*	update attributes
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
*	delete example
	```xml
	<delete id="deleteAuthor">
		delete from Author where id = #{id}
	</delete>
	```
*	delete attributes
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
---
*	sql - 这个元素可以被用来定义可重用的 SQL 代码段，可以包含在其他语句中。
	```xml
	<sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>

	<select id="selectUsers" resultType="map">
		select
		<include refid="userColumns"><property name="alias" value="t1"/></include>,
		<include refid="userColumns"><property name="alias" value="t2"/></include>
		from some_table t1
		cross join some_table t2
	</select>
	```
*	if - 动态 SQL 通常要做的事情是有条件地包含 where 子句的一部分。
	```xml
	<select id="findActiveBlogWithTitleLike" resultType="Blog">
		SELECT * FROM BLOG 
		WHERE state = ‘ACTIVE’ 
		<if test="title != null title != ''">
		AND title like #{title}
		</if>
	</select>
	```
*	choose, when, otherwise - 有些时候，我们不想用到所有的条件语句，而只想从中择其一二。
	```xml
	<select id="findActiveBlogLike" resultType="Blog">
		SELECT * FROM BLOG WHERE state = ‘ACTIVE’
		<choose>
			<when test="title != null title != ''">
				AND title like #{title}
			</when>
			<when test="author != null and author.name != null">
				AND author_name like #{author.name}
			</when>
			<otherwise>
				AND featured = 1
			</otherwise>
		</choose>
	</select>
	```
*	where - where元素知道只有在一个以上的if条件有值的情况下才去插入“WHERE”子句。而且，若最后的内容是“AND”或“OR”开头的，where元素也知道如何将他们去除。
	```xml
	<select id="findActiveBlogLike" resultType="Blog">
		SELECT * FROM BLOG 
		<where> 
			<if test="state != null">
				state = #{state}
			</if> 
			<if test="title != null">
				AND title like #{title}
			</if>
			<if test="author != null and author.name != null">
				AND author_name like #{author.name}
			</if>
		</where>
	</select>
	```
*	set - set 元素会动态前置 SET 关键字，同时也会消除无关的逗号。
	```xml
	<update id="updateAuthorIfNecessary">
		update Author
		<set>
			<if test="username != null and username != ''">username=#{username},</if>
			<if test="password != null and password != ''">password=#{password},</if>
			<if test="email != null and email != ''">email=#{email},</if>
			<if test="bio != null and bio != ''">bio=#{bio}</if>
		</set>
		where id=#{id}
	</update>
	```
*	trim -  trim 元素来定制我们想要的功能。prefixOverrides属性会忽略通过管道分隔的文本序列。它带来的结果就是所有在prefixOverrides属性中指定的内容将被移除，并且插入 prefix 属性中指定的内容。
	```xml
	<trim prefix="WHERE" prefixOverrides="AND |OR ">
		<!--equivalent to WHERE tag-->
		... 
	</trim>
	<trim prefix="SET" suffixOverrides=",">
		<!--equivalent to SET tag -->
		...
	</trim>
*	foreach - 对一个集合进行遍历。
	```xml
	<select id="selectPostIn" resultType="domain.blog.Post">
		SELECT *
		FROM POST P
		WHERE ID in
		<foreach item="item" index="index" collection="list" open="(" separator="," close=")">
			#{item}
		</foreach>
	</select>
	```
*	bind - bind 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文。
	```xml
	<select id="selectBlogsLike" resultType="Blog">
		<bind name="pattern" value="'%' + _parameter.getTitle() + '%'" />
		SELECT * FROM BLOG
		WHERE title LIKE #{pattern}
	</select>
	```
