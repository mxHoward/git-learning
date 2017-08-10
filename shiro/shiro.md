# Apache Shiro #

## Configuraiton ##
---
### Programmatic Configuration ###
  
基于代码的Shiro配置，简单易用。  
缺点也显而易见：和java类紧耦合、改变配置就要重新编译程序等等。  

　　example:  
```java
//创建一个 SecurityManager 并使之可用最简单的方法就是创建一个org.apache.shiro.mgt.DefaultSecurityManager
Realm realm = new Realm();

SecurityManager securityManager = new DefaultSecurityManager(realm);

//使SecurityManager实例通过静态存储器对整个应用程序可见：
SecurityUtils.setSecurityManager(securityManager);

SessionDAO sessionDAO = new CustomSessionDAO();

((DefaultSessionManager)securityManager.getSessionManager()).setSessionDAO(sessionDAO);

//other configuration
```
  
### INI Configuration ###
  
基于文本的配置，不需要依靠代码就可进行修改。
  
　　从INI资源创建SecurityManager：
```java
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.util.Factory;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.config.IniSecurityManagerFactory;

//...

Factory<SecurityManager> factory = new IniSecurityManagerFactory("classpath:shiro.ini");
SecurityManager securityManager = factory.getInstance();
SecurityUtils.setSecurityManager(securityManager);
```
　　shiro.ini：
```
# =======================
# Shiro INI configuration
# =======================

[main]
# Objects and their properties are defined here, 
# Such as the securityManager, Realms and anything
# else needed to build the SecurityManager

[users]
# The 'users' section is for simple deployments
# when you only need a small number of statically-defined 
# set of User accounts.

[roles]
# The 'roles' section is for simple deployments
# when you only need a small number of statically-defined roles.

[urls]
# The 'urls' section is used for url-based security
```
  
INI Sections
*	[main]  
	[main]区域是配置程序 SecurityManager 实例及其支撑组件的地方，如 Realm。  
	使用键-值对的形式。  
	eg：
	```
	[main]
	sha256Matcher = org.apache.shiro.authc.credential.Sha256CredentialsMatcher

	# defining an object myRealm
	myRealm = com.company.security.shiro.DatabaseRealm
	
	# primitive values
	myRealm.connectionTimeout = 30000
	myRealm.username = jsmith
	myRealm.password = secret

	# reference values
	myRealm.credentialsMatcher = $sha256Matcher

	# nested properties
	securityManager.sessionManager.globalSessionTimeout = 1800000
	```
  
*	[users]  
	[users]区域允许你定义一组静态的用户帐号，这对于那些只有少数用户帐号并且用户帐号不需要在运行时动态创建的环境来说非常有用。  
	eg：
	```
	[users]
	admin = secret
	lonestarr = vespa, goodguy, schwartz
	darkhelmet = ludicrousspeed, badguy, schwartz
	```  
	__Line Format__：username = password, rolename1, rolename2, ... rolenameN
	-	等号左边的值是用户名；
	-	等号右侧第一个值是用户密码，密码是必须的；
	-	密码之后用逗号分割的值是赋予用户的角色名，角色名是可选的。
	__Encrypting passwords__:  
	eg:
	```
	[main]
	...
	sha256Matcher = org.apache.shiro.authc.credential.Sha256CredentialsMatcher
	...
	# to tell the credentilasMatcher the encrypting algorithm you use.
	iniRealm.credentialsMatcher = $sha256Matcher
	...

	[users]
	# user1 = sha256-hashed-hex-encoded password, role1, role2, ...
	user1 = 2bb80d537b1da3e38bd30361aa855686bde0eacd7162fef6a25fe97bf527a25b, role1, role2, ...
	```  

*	[roles]  
	[roles]区域允许你将权限和在[users]定义的角色对应起来，同样的，这对于那些只有少数用户帐号并且用户帐号不需要在运行时动态创建的环境来说非常有用。
	eg：
	```
	[roles]
	# 'admin' role has all permissions, indicated by the wildcard '*'
	admin = *
	# The 'schwartz' role can do anything (*) with any lightsaber:
	schwartz = lightsaber:*
	# The 'goodguy' role is allowed to 'drive' (action) the winnebago (type) with
	# license plate 'eagle5' (instance specific id)
	goodguy = winnebago:drive:eagle5
	```  
	__Line Format__：rolename = permissionDefinition1, permissionDefinition2, ..., permissionDefinitionN

*	[urls]  
	//TODO

## SecurityManager ##
---
### Authentication ###

　　认证（Authentication）：身份验证的过程--也就是证明一个用户的真实身份。为了证明用户身份，需要提供系统理解和相信的身份信息和证据。
需要通过向 Shiro 提供用户的身份（principals）和证明（credentials ）来判定是否和系统所要求的匹配。Principals(身份)是Subject的“标识属性”，可以是任何与Subject相关的标识。Credentials(证明) 通常是只有 Subject 知道的机密内容，用来证明他们真正拥有所需的身份。  
Subject 验证的过程可以有效地划分分以下三个步骤：
1.	收集 Subject 提交的身份和证明；
	```java
	//最常用的情况是 username/password 对:
	UsernamePasswordToken token = new UsernamePasswordToken(username, password);
	//”Remember Me” 功能是内建的
	token.setRememberMe(true);
	```
2.	向 Authentication 提交身份和证明；
	```java
	Subject currentUser = SecurityUtils.getSubject();//get current user
	currentUser.login(token);//log in while authenticating 
	```
3.	如果提交的内容正确，允许访问，否则重新尝试验证或阻止访问。
	```java
	try {
	    currentUser.login(token);
	} catch ( UnknownAccountException uae ) { ...
	} catch ( IncorrectCredentialsException ice ) { ...
	} catch ( LockedAccountException lae ) { ...
	} catch ( ExcessiveAttemptsException eae ) { ...
	} ... 捕获你自己的异常 ...
	} catch ( AuthenticationException ae ) {
	    //未预计的错误?
	}

	//没问题，继续
	```
*	AuthenticationStrategy  
	当一个程序中定义了两个或多个 realm 时，ModularRealmAuthenticator 使用一个内部的AuthenticationStrategy 组件来决定一个验证是否成功。  
	AuthenticationStrategy 是一个 stateless 的组件，在整个验证过程中在被用到4次（在这4次活动中需要必要的 state 时，state 将作为方法参数传递）  
	-	在任何 Realms 被执行之前；
	-	在某个的 Realm 的 getAuthenticationInfo 方法调用之前；
	-	在某个的 Realm 的 getAuthenticationInfo 方法调用之后；
	-	在所有的 Realm 被执行之后。  
	Shiro有3个具体的AuthenticationStrategy实现：
	-	AtLeastOneSuccessfulStrategy
	-	FirstSuccessfulStrategy
	-	AllSuccessfulStrategy
	默认使用 AtLeastOneSuccessfulStrategy 实现，这也是最常用的策略，然而你也可以配置你希望的不同的策略。
	```
	shiro.ini
	[main]
	...
	authcStrategy = org.apache.shiro.authc.pam.FirstSuccessfulStrategy

	securityManager.authenticator.authenticationStrategy = $authcStrategy

	...
	```  

### Authorization ###
  
#### Elements of Authorization ####
　　授权，亦即访问控制，是管理资源访问的过程，换言之，也就是控制在一个程序中“谁”有权利访问“什么”。授权有三个核心元素，在 Shiro 中我们经常要用到它们：权限（permissions）、角色（roles）和用户（users）。  
*	Permissions  
	权限是 Apache Shiro 中安全策略最基本的元素，它们是一组关于行为的基本指令，以明确表示在一个程序中什么可以做。权限只描述行为
	权限指令只描述行为（和资源相关的动作），并不关心“谁”有能力执行这个动作。
*	Roles  
	角色是一个实体名，代表一组行为或职责，这些行为在程序中转换为你可以或者不能做的事情。角色通常赋给用户帐号，关联后，用户就可以“做”属于不同角色的事情。
	有两种有效的角色指定方式，Shiro 都支持：  
	-	权限隐含于角色中：大部分用户将权限隐含于创建的角色中。（不推荐）
	-	明确为角色指定权限：明确为角色指定权限本质上是一组权限指令的名称集。（推荐）
*	Users  
	一个用户本质上是程序中的“谁”，如同我们前面提到的，Subject实际上是shiro的“用户”。用户（Subjects）通过与角色或权限关联确定是否被允许执行程序内特定的动作，程序数据模型确切定义了Subject是否允许做什么事情。
  
#### Authorizing Subjects ####

在 Shiro 中执行授权可以有三种途径：
*	程序代码--你可以在你的 JAVA 代码中执行用类似于 if 和 else 的结构来执行权限检查。  
	直接在程序中为当前 Subject 实例检查授权可能是最简单也最常用的方法。
	-	Role-Based Authorizaiton
		-	Role Checks 角色检查 - hasRole(String roleName), hasRoles(List<String> roleNames)  
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			if (currentUser.hasRole("administrator")) {
			    //显示 admin 按钮
			} else {
			    //不显示按钮?  灰色吗？
			}
			```
		-	Role Assertions 角色判断 - checkRole(String roleName), checkRoles(List<String> roleNames)
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			//保证当前用户是一个银行出纳员
			//因此允许开立帐户：
			currentUser.checkRole("bankTeller");
			openBankAccount();
			```  

	-	Permission-Based Authorization
		-	Permission Checks 权限检查
			1.Object-based Permission Checks 基于对象的权限检查  
			isPermitted(Permission perm), isPermitted(List<Permission> perms), isPermittedAll(Collection<Permission> perms)  
			eg:
			```java
			Permission printPermission = new PrinterPermission("laserjet4400n", "print");
			Subject currentUser = SecurityUtils.getSubject();
			if (currentUser.isPermitted(printPermission)) {
			    //显示 打印 按钮
			} else {
			    //不显示按钮?  灰色吗？
			}
			```

			2.String-based permission checks 基于字符串的权限检查  
			isPermitted(String perm), isPermitted(List<String> perms), isPermittedAll(Collection<String> perms)  
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			Permission p = new WildcardPermission("printer:print:laserjet4400n");
			if (currentUser.isPermitted(p) {
			    //显示 打印 按钮
			} else {
			    //不显示按钮?  灰色吗？
			}
			```
		-	Permission Assertions 权限判断
			1.Object-based Permission Assertions 基于对象的权限判断  
			checkPermission(Permission perm), checkPermissions(List<Permission> perms)  
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			//担保允许当前用户
			//开一个银行帐户：
			Permission p = new AccountPermission("open");
			currentUser.checkPermission(p);
			openBankAccount();
			```

			2.String-based permission Assertions 基于字符串的权限判断 
			checkPermission(String perm), checkPermissions(List<String> perms)  
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			//担保允许当前用户
			//开一个银行帐户：
			currentUser.checkPermission("account:open");
			openBankAccount();
			```
*	JDK 注解--你可以在你的 JAVA 方法上附加权限注解。
*	JSP/GSP 标签--你可以基于角色和权限控制 JSP 或 GSP 页面输出内容。

### Session Management ###

### Cache Management ###

### Realm Coordination ###

### Event Propogation ###

### "Remember Me" Services ###

### Subject Creation ###

### Log Out ###