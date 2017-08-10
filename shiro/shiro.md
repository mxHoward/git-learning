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
	
## SecurityManager ##
---
### Authentication ###

### Authorization ###

### Session Management ###

### Cache Management ###

### Realm Coordination ###

### Event Propogation ###

### "Remember Me" Services ###

### Subject Creation ###

### Log Out ###