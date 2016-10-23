---
title: 搭建S2SH框架
date: 2016-10-15 13:15:55
categories: [java]
tags: [Struts2、Spring、Hibernate]
---

从工作到现在一年多，从没搭建过S2SH（Struts2+Spring+Hibernate）框架，虽然也感觉应该是没什么难度，但是没有真正动手搭建过也不好意思说自己就真的会。于是，上天就给了我这么个机会，降大任于我，要我从搭建框架开始，苦我心智，劳我筋骨……然后，就有了这篇记录我如何快速搭建一个S2SH的教程<!-- more-->

# 一、准备阶段

其实也没什么好准备的，确定IDE可以正常创建一个Dynamic Web项目，下载S2SH相关的jar包导入就可以开始配置了。这一步基本不会遇到什么麻烦。

# 二、配置Struts2

在项目中的web.xml中添加Struts2的配置，内容如下
```
<!-- struts2配置 -->
<filter>
 <filter-name>struts2</filter-name>
 <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
  <filter-name>struts2</filter-name>
  <url-pattern>/*</url-pattern>
</filter-mapping>
```

配置完后就可以创建struts配置文件了，可以添加下Action方法，页面上调用该action方法进行测试。这里就不多叙述了。
```
<?xml version="1.0" encoding="UTF-8" ?> 
<!DOCTYPE struts PUBLIC 
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN" 
    "http://struts.apache.org/dtds/struts-2.0.dtd"> 
<struts> 
    <package name="test" namespace="/" extends="struts-default">
        <action name="openTestPage" class="com.vipmangement.function.test.action.functionTestAction"
            method="testPage">
            <result name="success">/WEB-INF/page/test.jsp</result>
        </action>
    </package>
</struts> 
```

# 三、配置Spring

Spring的配置和Struts2差不多，也是在web.xml中
```
    <!-- spring  监听器-->
<context-param>
   <param-name>contextConfigLocation</param-name>
   <param-value>/WEB-INF/classes/applicationContext.xml,classpath*:com/*/*Context.xml,classpath*:com/*/*/*Context.xml</param-value>
</context-param>
<listener>
   <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>  
```

需要注意的是，如果像上面这么配置的话，那么spring的配置文件需要写在src目录下，名字为applicationContext.xml。这样子生成部署包后，该文件才会存在于**/WEB-INF/classes/applicationContext.xml**。

Spring配置完监听后，就可以直接在配置文件中配置使用了,配置格式如下
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-2.5.xsd
">

<bean id="functionTestAction" class="ccom.vipmangement.function.test.action.functionTestAction" scope="prototype">
            <property name="test" ref="test"></property>
    </bean>
</beans>  
```

# 四、配置Hinbernate

hibernate有两种配置方式，一种是单独写配置文件，一种是直接配置到Spring中。

## 1、添加配置文件

- 在src目录下创建一个hibernate.xml,
```
<!DOCTYPE hibernate-configuration PUBLIC
 "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
 "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
    <!-- 这边我连接的是mysql数据库-->
        <property name="dialect">
            org.hibernate.dialect.MySQLDialect
        </property>
        <property name="connection.url">
<!--localhost/数据库名 -->
            jdbc:mysql://localhost/salevipmanagementsystem
        </property>
        <property name="connection.username">jarvis</property>
        <property name="connection.password">jarvis</property>
        <property name="connection.driver_class">
            com.mysql.jdbc.Driver
        </property>
        <property name="myeclipse.connection.profile">mysql</property>
  <mapping resource="com/vipmangement/function/test/bean/UserBean.hbm.xml"/> 
    </session-factory>
</hibernate-configuration>
```

- 创建一个用于读取数据的sessionFactory
```
package com.vipmangement.function.test.action;

import org.hibernate.HibernateException;
import org.hibernate.Session;
import org.hibernate.cfg.Configuration;

/**
 * Configures and provides access to Hibernate sessions, tied to the
 * current thread of execution.  Follows the Thread Local Session
 * pattern, see {@link http://hibernate.org/42.html }.
 */
public class HibernateSessionFactory {

    /** 
     * Location of hibernate.cfg.xml file.
     * Location should be on the classpath as Hibernate uses  
     * #resourceAsStream style lookup for its configuration file. 
     * The default classpath location of the hibernate config file is 
     * in the default package. Use #setConfigFile() to update 
     * the location of the configuration file for the current session.   
     */
    private static String CONFIG_FILE_LOCATION = "/hibernate.xml";
	private static final ThreadLocal<Session> threadLocal = new ThreadLocal<Session>();
    private  static Configuration configuration = new Configuration();    
    private static org.hibernate.SessionFactory sessionFactory;
    private static String configFile = CONFIG_FILE_LOCATION;

	static {
    	try {
			configuration.configure(configFile);
			sessionFactory = configuration.buildSessionFactory();
		} catch (Exception e) {
			System.err
					.println("%%%% Error Creating SessionFactory %%%%");
			e.printStackTrace();
		}
    }
    private HibernateSessionFactory() {
    }
	
	/**
     * Returns the ThreadLocal Session instance.  Lazy initialize
     * the <code>SessionFactory</code> if needed.
     *
     *  @return Session
     *  @throws HibernateException
     */
    public static Session getSession() throws HibernateException {
        Session session = (Session) threadLocal.get();

		if (session == null || !session.isOpen()) {
			if (sessionFactory == null) {
				rebuildSessionFactory();
			}
			session = (sessionFactory != null) ? sessionFactory.openSession()
					: null;
			threadLocal.set(session);
		}

        return session;
    }

	/**
     *  Rebuild hibernate session factory
     *
     */
	public static void rebuildSessionFactory() {
		try {
			configuration.configure(configFile);
			sessionFactory = configuration.buildSessionFactory();
		} catch (Exception e) {
			System.err
					.println("%%%% Error Creating SessionFactory %%%%");
			e.printStackTrace();
		}
	}

	/**
     *  Close the single hibernate session instance.
     *
     *  @throws HibernateException
     */
    public static void closeSession() throws HibernateException {
        Session session = (Session) threadLocal.get();
        threadLocal.set(null);

        if (session != null) {
            session.close();
        }
    }

	/**
     *  return session factory
     *
     */
	public static org.hibernate.SessionFactory getSessionFactory() {
		return sessionFactory;
	}

	/**
     *  return session factory
     *
     *	session factory will be rebuilded in the next call
     */
	public static void setConfigFile(String configFile) {
		HibernateSessionFactory.configFile = configFile;
		sessionFactory = null;
	}

	/**
     *  return hibernate configuration
     *
     */
	public static Configuration getConfiguration() {
		return configuration;
	}

}
```

- 创建表的映射

假定我创建了一张名为TABLE的表，表中有id,userid,pwd三个字段.

1. 首先，我需要创建一个bean

UserBean.java

```
public class UserBean {

	private int id;
	private String userName;
	private String password;
	public int getId() {
		return id;
	}
	public void setId(int id) {
		this.id = id;
	}
	public String getUserName() {
		return userName;
	}
	public void setUserName(String userName) {
		this.userName = userName;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	@Override
	public String toString() {
		return "UserBean [id=" + id + ", password=" + password + ", userName="
				+ userName + "]";
	}
	
}

```
2. 接着，在bean目录下创建配置文件UserBean.hbm.xml
```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>

    <class name="com.test.bean.UserBean" table="TABLE" schema="databaseName">
        <id name="id" type="java.lang.Integer">
            <column name="id" />
        </id>
        <property name="userName" type="java.lang.String">
            <column name="userid" length="20" />
        </property>
        <property name="password" type="java.lang.String">
            <column name="pwd" length="50" />
        </property>
    </class>
</hibernate-mapping>
```

3. 使用hibernate提供的方法调用数据库
```
public static void main(String[] args) {
    Session session=HibernateSessionFactory.getSession();

    Query query=session.createQuery("from UserBean where id=1");
    List list=query.list();
    UserBean kc1=(UserBean)list.get(0);
    System.out.println(kc1.getUserName());
    HibernateSessionFactory.closeSession();
}  
```

## 2、在Spring中配置Hinbernate，

在applicationContext.xml中添加如下配置，配置的还是mysql
```
<!-- 数据源 -->
	<bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName"
			value="com.mysql.jdbc.Driver">
		</property>
		<property name="url"
			value="jdbc:mysql://localhost/dataBaseName">
		</property>

		<property name="username" value="admin"></property>
		<property name="password" value="admin"></property>

	</bean>
	<!-- dataSource注入到sessionFactory -->
	<bean id="sessionFactory"
		class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
		<property name="dataSource">
			<ref bean="dataSource" />
		</property>
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">
					org.hibernate.dialect.MySQLDialect
				</prop>
				<prop key="hibernate.show_sql">true</prop>
			</props>
		</property>
		<property name="mappingResources">
			<list>
				<value>com/test/bean/UserBean.hbm.xml</value>
				
			</list>
		</property></bean>
```

接着，写好bean和bean.hbm.xml映射文件，就可以使用了。要使用的类需继承HibernateDaoSupport，使用方法如下
```
public class test extends HibernateDaoSupport {

	public void test(){
			
		String queryString="from UserBean where id=1";
		List aList = getHibernateTemplate().find(queryString);
		UserBean kc1=(UserBean)aList.get(0);
		System.out.println(kc1.getUserName());
	  
	}
}

```

# 五、总结

搭建一个S2SH框架基本没什么难点，和普通的jar包使用上差不多。而搭建一个这样的框架，好处在于可以用它搭建出一个完整的web项目来。当然，这样的框架还有很多，比如S2SM（Struts2+Spring+mybatis）等。至于它们之间的适用范围和差异，就留待以后接触到了再研究吧。