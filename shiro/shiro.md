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
*	Authentication Strategy  
	当一个程序中定义了两个或多个 realm 时，ModularRealmAuthenticator 使用一个内部的AuthenticationStrategy 组件来决定一个验证是否成功。  
	AuthenticationStrategy 是一个 stateless 的组件，在整个验证过程中在被用到4次：
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
  
#### Permissions ####
  
*	Wildcard Permissions __通配符的权限__  
	`subject.isPermitted("queryPrinter");` 基本相当于 `subject.isPermitted( new WildcardPermission("queryPrinter") )`  
	-	Mulitple Parts __多个部分__  
		-	Mulitple Values __多值__  
			`printer:print,query`
		-	All Valuse __所有值__  
			`printer:*  `
			`*:view`
	-	Instance-Level Access Control __实例级的访问控制__  
		`printer:query:lp7200` - 定义查询拥有ID lp7200 的打印机的行为  
		`printer:print:epsoncolor` - 定义打印到拥有 ID epsoncolor 的打印机的行为  
		`printer:print:*` - 定义所有打印机的打印行为  
		`printer:*:*` - 所有打印机的所有操作行为  
		`printer:*:lp7200` - 一台打印机的所有操作行为  
  
*	Checking Permissions __检查权限__  
	在执行权限检查时，尽可能使用权限字符串。应用程序将决定检查哪些有意义，但一般情况下，越具体越好。  
	运行时权限检查应该尽可能的具体，但权限分配可以较为普通。  
  
#### Authorizing Subjects ####

在 Shiro 中执行授权可以有三种途径：
*	程序代码--你可以在你的 JAVA 代码中执行用类似于 if 和 else 的结构来执行权限检查。  
	直接在程序中为当前 Subject 实例检查授权可能是最简单也最常用的方法。
	-	Role-Based Authorizaiton
		-	Role Checks __角色检查__ - hasRole(String roleName), hasRoles(List<String> roleNames)  
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			if (currentUser.hasRole("administrator")) {
			    //显示 admin 按钮
			} else {
			    //不显示按钮?  灰色吗？
			}
			```
		-	Role Assertions __角色判断__ - checkRole(String roleName), checkRoles(List<String> roleNames)  
			eg:
			```java
			Subject currentUser = SecurityUtils.getSubject();
			//保证当前用户是一个银行出纳员
			//因此允许开立帐户：
			currentUser.checkRole("bankTeller");
			openBankAccount();
			```  

	-	Permission-Based Authorization
		-	Permission Checks __权限检查__  
			1.Object-based Permission Checks __基于对象的权限检查__  
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

			2.String-based permission checks __基于字符串的权限检查__  
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
		-	Permission Assertions __权限判断__  
			1.Object-based Permission Assertions __基于对象的权限判断__  
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

			2.String-based permission Assertions __基于字符串的权限判断__  
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
	基于注解的授权控制，除了 Subject 的 API 之外，Shiro提供了一个 Java 5 的注解集。  
	-	RequiresAuthentication 注解  
		RequiresAuthentication 注解表示在访问或调用被注解的类/实例/方法时，要求 Subject 在当前的 session中已经被验证。  
		eg:
		```java
		@RequiresAuthentication
		public void updateAccount(Account userAccount) {
		    //这个方法只会被调用在
		    //Subject 保证被认证的情况下
		    //...
		}
		//the above code is equivanlent to the following code
		public void updateAccount(Account userAccount) {
		    if (!SecurityUtils.getSubject().isAuthenticated()) {
		        throw new AuthorizationException(...);
		    }
		    //这里 Subject 保证被认证的情况下
		    //...
		}
		```
	-	RequiresGuest 注解  
		RequiresGuest 注解表示要求当前Subject是一个“guest(访客)”，也就是，在访问或调用被注解的类/实例/方法时，他们没有被认证或者在被前一个Session 记住。  
		eg:
		```java
		@RequiresGuest
		public void signUp(User newUser) {
		    //这个方法只会被调用在
		    //Subject 未知/匿名的情况下
		    //...
		}
		//the above code is equivanlent to the following code
		public void signUp(User newUser) {
		    Subject currentUser = SecurityUtils.getSubject();
		    PrincipalCollection principals = currentUser.getPrincipals();
		    if (principals != null && !principals.isEmpty()) {
		        //已知的身份 - 不是 guest（访客）:
		        throw new AuthorizationException(...);
		    }
		    //在这里 Subject 确保是一个 'guest（访客）'
		    //...
		}
		```
	-	RequiresPermissions 注解  
		RequiresPermissions 注解表示要求当前Subject在执行被注解的方法时具备一个或多个对应的权限。  
		eg:
		```java
		@RequiresPermissions("account:create")
		public void createAccount(Account account) {
		    //这个方法只会被调用在
		    //Subject 允许创建一个 account 的情况下
		   // ...
		}
		//the above code is equivanlent to the following code
		public void createAccount(Account account) {
		    Subject currentUser = SecurityUtils.getSubject();
		    if (!subject.isPermitted("account:create")) {
		        throw new AuthorizationException(...);
		    }
		    //在这里 Subject 确保是允许
		    //...
		}
		```
	-	RequiresRoles 注解  
		RequiresRoles 注解表示要求当前Subject在执行被注解的方法时具备所有的角色，否则将抛出 AuthorizationException 异常。  
		eg:
		```java
		@RequiresRoles("administrator")
		public void deleteUser(User user) {
		    //这个方法只会被 administrator 调用 
		    //...
		}
		//the above code is equivanlent to the following code
		public void deleteUser(User user) {
		    Subject currentUser = SecurityUtils.getSubject();
		    if (!subject.hasRole("administrator")) {
		        throw new AuthorizationException(...);
		    }
		    //Subject 确保是一个 'administrator'
		    //...
		}
		```
	-	RequiresUser 注解  
		RequiresUser* 注解表示要求在访问或调用被注解的类/实例/方法时，当前 Subject 是一个程序用户，“程序用户”是一个已知身份的 Subject，或者在当前 Session 中被验证过或者在以前的 Session 中被记住过。  
		eg:
		```java
		@RequiresUser
		public void updateAccount(Account account) {
		    //这个方法只会被 'user' 调用 
		    //i.e. Subject 是一个已知的身份with a known identity
		    //...
		}
		//the above code is equivanlent to the following code
		public void updateAccount(Account account) {
		    Subject currentUser = SecurityUtils.getSubject();
		    PrincipalCollection principals = currentUser.getPrincipals();
		    if (principals == null || principals.isEmpty()) {
		        //无身份 - 他们是匿名的，不被允许
		        throw new AuthorizationException(...);
		    }
		    //Subject 确保是一个已知的身份
		    //...
		}
		```
*	JSP/GSP 标签--你可以基于角色和权限控制 JSP 或 GSP 页面输出内容。  
	//TODO  

#### Authorization Sequence ####
  
　　授权操作的步骤：
1.	程序或框架代码调用一个 Subject 的hasRole*、checkRole*、 isPermitted*或者 checkPermission*方法，传递所需的权限或角色；
2.	Subject实例，通常是一个DelegatingSubject（或子类），通过调用securityManager与各hasRole*、checkRole*、isPermitted*或checkPermission*				基本一致的方法将权限或角色传递给程序的 SecurityManager(实现了 org.apache.shiro.authz.Authorizer 接口)；
3.	SecurityManager作为一个基本的“保护伞”组件,接替/代表其内部org.apache.shiro.authz.Authorizer实例通过调用authorizer的各自的hasRole*,checkRole*,isPermitted*, 	或checkPermission*方法。authorizer 默认情况下是一个实例 ModularRealmAuthorizer 支持协调一个或多个实例 Realm 在任何授权操作实例；
4.	检查每一个被配置的 Realm 是否实现相同的 Authorizer接口，如果是，Realm 自己的各 hasRole*、checkRole*、 isPermitted* 或 checkPermission* 方法被调用。
  

### Realms ###
  
#### Realm Configuration ####
　　如果使用 Shiro 的 ini 配置文件，你可以在[main]区域内像配置其它对象一样定义和引用Realms，但是 Realm 在 secrityManager上的配置有两种方式：明确方式和隐含方式。  

*	Explicit Assignment __明确指定(显式)__（推荐）  
	eg:
	```
	fooRealm = com.company.foo.Realm
	barRealm = com.company.another.Realm
	bazRealm = com.company.baz.Realm

	securityManager.realms = $fooRealm, $barRealm, $bazRealm
	```  
	明确设置是确定性的，可以非常确切地知道哪个 realm 在使用并且知道它们执行的顺序。  
*	Implicit Assignment __隐含方式(隐式)__（不推荐）  
	eg:
	```
	blahRealm = com.company.blah.Realm
	fooRealm = com.company.foo.Realm
	barRealm = com.company.another.Realm

	# no securityManager.realms assignment here
	```
  
#### Realm Authentication ####
  
*	Supporting Authentication Tokens  
	正如在认证流程中提到的，在一个Realm执行一个验证尝试之前，它的supports方法被调用。只有在返回值为true的时候它的getAuthenticationInfo(token)方法才会执行。
*	Handling supported Authentication Tokens  
	如果一个Realm支持提交的验证令牌，验证将调用 Realm 的getAuthenticationInfo(token)) 方法，这是Realm 使用后台数据进行验证的一次有效尝试，顺序执行以下动作：  
	1.	检查主要 principal (身份)令牌（用户身份信息）；
	2.	基于主要 principal (信息)，在数据源中查找对应的用户数据；
	3.	确定令牌支持的 credentials (凭证数据)和存储的数据相符；
	4.	如果凭证相符，返回一个AuthenticationInfo实例，里面封装了 Shiro 可以理解的用户数据。
	5.	如果证据不符，抛出 AuthenticationException异常。
  
*	Credentials Matching __凭证匹配__  
	-	Simple Equality Check __简单证明匹配__  
		所有Shiro的开箱即用Realm默认使用一个SimpleCredentialsMatcher，SimpleCredentialsMatcher对存储的用户凭证和从AuthenticationToken提交的用户凭证直接执行相等检查。例如，如果提交了一个UsernamePasswordToken，SimpleCredentialsMatcher检查提交的密码与存储的密码是否完全相等。
	-	Hashing Credentials __哈希凭证__  
		取代将凭证按它们原始形式存储并执行原始数据的对比，存储终端用户的凭证（如密码）更安全的办法是在存储数据之前，先进行hash运算。这确保终端用户的凭证不会以他们原始的形式存储，没有人能知道其原始值。与明文原始比较相比这是一种更为安全的做法，有安全意识的程序会更喜欢这种方法。要支持这种加密的hash策略，Shiro为Realm配置提供了一个HashedCredentialsMatcher实现替代之前的SimpleCredentialsMatcher。  
		eg:
		```java
		import org.apache.shiro.crypto.hash.Sha256Hash;
		import org.apache.shiro.crypto.RandomNumberGenerator;
		import org.apache.shiro.crypto.SecureRandomNumberGenerator;
		//...
		//我们将使用一个随机数发生器产生的盐。
		//这比使用一个用户名作为盐或不使用盐更加安全。
		//Shiro使这个实现变得容易。
		//
		//注意一个正常的应用程序将引用属性而
		//不是每次创建一个新的RNG：
		RandomNumberGenerator rng = new SecureRandomNumberGenerator();
		Object salt = rng.nextBytes();
		//我们的纯文本密码经过散列随机盐和多次迭代，
		//得到Base64编码的值（比Hex需要较少的空间）：
		String hashedPasswordBase64 = new Sha256Hash(plainTextPassword, salt, 1024).toBase64();
		User user = new User(username, hashedPasswordBase64);
		//在新帐户保存盐。该 HashedCredentialsMatcher
		//稍后再的登录尝试的时候会处理它：
		user.setPasswordSalt(salt);
		userDAO.create(user);
		```
		在shiro.ini文件中配置CredentialsMatcher:  
		```
		[main]
		...
		credentialsMatcher = org.apache.shiro.authc.credential.Sha256CredentialsMatcher
		# base64 编码, 例子中没有 hex:
		credentialsMatcher.storedCredentialsHexEncoded = false
		credentialsMatcher.hashIterations = 1024
		# 下面属性只在 Shiro 1.0 需要，在 1.1 及以后版本移除了:
		credentialsMatcher.hashSalted = true

		...
		myRealm = com.company.....
		myRealm.credentialsMatcher = $credentialsMatcher
		...
		```

### Session Management ###
  
#### Using Session ####
  
```java
Subject currentUser = SecurityUtils.getSubject();

Session session = currentUser.getSession();
session.setAttribute( "someKey", someValue);
```

#### The Sessions Manager ####
  
　　SessionManager在应用程序中为所有的 subject 管理Session —— 创建，删除，inactivity(失效)及验证，等等。像其他被SecurityManager管理的组件一样，SessionManager可以通过 JavaBean 风格的getter/setter方法在所有Shiro默认
SecurityManager实现（getSessionManager()/setSessionManager()）上获取或设置值。  
或者例如，如果在使用 shiro.ini 配置：
```
[main]
...
sessionManager = com.foo.my.SessionManagerImplementation
securityManager.sessionManager = $sessionManager
```
  
*	Session Timeout __超时__  
	默认地，Shiro 的 SessionManager 实现默认是 30 分钟会话超时。也可以在shiro.ini中设置超时时间：  
	```
	[main]
	...
	# 3,600,000 milliseconds = 1 hour
	securityManager.sessionManager.globalSessionTimeout = 3600000
	```  
*	Session Listeners  
	Shiro支持SessionListener概念来允许你对发生的重要会话作出反应。你可以实现SessionListener接口（或扩展易用的SessionListenerAdapter）并与相应的会话操作作出反应。 由于默认的SessionManager sessionListeners属性是一个集合，你可以对SessionManager配置一个或多个listener实 现，就像其他在shiro.ini中的集合一样：  
	```
	[main]
	...
	aSessionListener = com.foo.my.SessionListener
	anotherSessionListener = com.foo.my.OtherSessionListener

	securityManager.sessionManager.sessionListeners = $aSessionListener, $anotherSessionListener, etc.
	```  
*	Session Storage __存储__  
	每当一个会话被创建或更新时，它的数据需要持久化到一个存储位置以便它能够被稍后的应用程序访问。同样地，当一个会话失效且不再被使用时，它需要从存储中删除以便会话数据存储空间不会被耗尽。SessionManager实现委托这些Create/Read/Update/Delete(CRUD)操作为内部组件，同时，SessionDAO，反映了数据访问对象（DAO）设计模式。SessionDAO的权力是你能够实现该接口来与你想要的任何数据存储进行通信。  
	在shiro.ini中配置：
	```
	[main]
	...
	sessionDAO = com.foo.my.SessionDAO
	securityManager.sessionManager.sessionDAO = $sessionDAO
	```
*	EHCache SessionDAO  
	EHCache 默认是没有启用的，但如果不打算实现自己的SessionDAO，那么强烈地建议为Shiro的SessionManagerment启用EHCache支持。EHCache SessionDAO将会在内存中保存会话，并支持溢出到磁盘。  
*	Session Validation & Scheduling  
	Sessions 必须被验证，这样任何无效(过期或停止)的会话能够从会话数据存储中删除。这保证了数据存储不会由于不能再次使用的会话而导致写入超时。由于性能上的原因，仅仅在Sessions被访问（也就是subject.getSession()）时验证它们是否停止或过期。这意味着， 如果没有额外的定期验证，Session orphans(孤儿)将会开始填充会话数据存储。因此，为了防止堆放孤儿，SessionManager实现支持SessionValidationScheduler的概念。SessionValidationScheduler负责定期地验证会话以确保它们是否需要清理。  
	-	Default SessionValidationScheduler  
		默认可用的 SessionValidationScheduler 在所有环境中都是ExecutorServiceSessionValidationScheduler，它使用JDK ScheduledExecutorService来控制验证频率。默认地，该实现每小时执行一次验证。
	-	Custom SessionValidationScheduler __自定义SessionValidationScheduler__  
		如果希望提供一个自定义的 SessionValidationScheduler 实现，可以指定它作为默认的 SessionManager 实例的一个属性。例如，在shiro.ini 中：
		```
		[main]
		…
		sessionValidationScheduler = com.foo.my.SessionValidationScheduler
		securityManager.sessionManager.sessionValidationScheduler = $sessionValidationScheduler
		```
	-	Disabling SessionValidation __禁用SessionValidation__  
		在某些情况下，你可能希望禁用会话验证项，由于你建立了一个超出了Shiro 控制的进程来为你执行验证。
		```securityManager.sessionManager.sessionValidationSchedulerEnabled = false```


#### Sessions Clustering ####
  
*	Distributed Caches __分布式缓存__  
	分布式缓存比如 Ehcache + TerraCotta , GigaSpaces Oracle Coherence , memcached (和许多其他)已经解决 distributed-data-at-the-persistence-level(分布式数据持久层) 问题。 因此在 Shiro 会话使用集群配置如同使用分布式缓存一样简单。
*	EnterpriseCacheSessionDAO  
	Shiro 已经提供了SessionDAO的实现，将保存数据到企业/分布式缓存。EnterpriseCacheSessionDAO预计Shiro缓存或缓存管理器已经配置，所以它可以利用缓存机制。  
	例如：
	```
	#This implementation would use your preferred distributed caching product's APIs:
	activeSessionsCache = my.org.apache.shiro.cache.CacheImplementation

	sessionDAO = org.apache.shiro.session.mgt.eis.EnterpriseCacheSessionDAO
	sessionDAO.activeSessionsCache = $activeSessionsCache

	securityManager.sessionManager.sessionDAO = $sessionDAO
	```  
*	Ehcache + Terracotta  
	另一个分布式缓存解决方案。 
  
#### Sessions and Subject State ####
  
*	Stateful Applications (Sessions allowed) __有状态__  
	默认地，Shiro的SecurityManager实现使用一个Subject的Session作为一种策略来为接下来的引用存储Subject的身份ID（PrincipalCollection）和验证状态（subject.isAuthenticated()）。这通常发生在一个Subject 登录后或当一个 Subject 的身份 ID 通过Remember 服务被发现后。
*	Stateless Applications (Sessionless) __无状态__  
	虽然上述的默认策略对于大多数应用程序而言是很好的（通常是可取的），但这对于尝试尽可能无状态的应用程序来说是不合适的。许多无状态的架构规定在请求中不能存在持久状态，这种情况下的Sessions不会被允许（一个会话其本质代表了持久状态）。
*	Hybrid Approach  
	...


### Cache Management ###

### Event Propogation ###

### "Remember Me" Services ###

### Subject Creation ###

### Log Out ###