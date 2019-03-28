## Web应用基础介绍 ##
JSP、Servlet、Listener、Filter等都必须运行在web应用中。

### Web应用的基本项目结构 ###
web应用的项目结构：

	WebDemo
	-- WEB-INF
		-- classes
		-- lib
		-- web.xml
	-- .jsp文件

如上，WEB-INF是一个特殊的文件夹，Web容器（Tomcat）会包含该文件夹下的内容。客户端无法访问WEB-INF目录下的任何内容。

classes文件夹 存放生成的字节码.class文件

lib文件夹 存放Jar包文件

web.xml文件 用于配置web应用的组件信息等。

jsp文件可以放在Web应用的根目录下。

### web.xml文件 ###
web.xml被称为配置描述符。Java Web应用中的绝大部分组件（JSP、Servlet、Listener、Filter等）都可以通过web.xml文件配置管理。从Servlet 3.0开始，还可以通过注解来配置管理Web组件。
web.xml可配置管理的内容如下：

	配置JSP
	配置和管理Servlet
	配置和管理Listener
	配置和管理Filter
	配置标签库
	配置JSP属性
	
	配置和管理JAAS授权认证
	配置和管理资源引用
	配置Web应用首页

web.xml文件的根元素是<web-app>标签，Servlet 3.0规范为此标签新增了metadata-complete属性，为true时，Web应用将不会加载由注解配置的Web组件。

web.xml文件使用welcom-file-list元素配置web应用首页，该元素可包含多个首页，每个首页用welcom-file子元素配置。首页优先选用第一个配置的welcomm-file子元素。

每个Web容器也会提供一个系统的web.xml，用于描述所有Web应用共有的配置属性（Tomcat的系统web.xml在conf目录下）。

web.xml文件格式如下：

	<?xml version="1.0" encoding="ISO-8859-1"?>
	
	<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
	    http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	    version="3.1">

		<context-param>
			<param-name>driver</param-name>
			<param-value>com.mysql.jdbc.Driver</param-value>
		</context-param>

		<filter>
	        <filter-name>....</filter-name>
	        <filter-class>...</filter-class>
	        <async-supported>...</async-supported>
	        <init-param>
	            <param-name>...</param-name>
	            <param-value>...</param-value>
	        </init-param>
	    </filter>
	
		<filter-mapping>
	        <filter-name>...</filter-name>
	        <url-pattern>/*</url-pattern>
	    </filter-mapping>

		<listener>
	        <listener-class>...</listener-class>
	    </listener>

		<servlet>
	        <servlet-name>....</servlet-name>
	        <servlet-class>...</servlet-class>
		    <init-param>
	            <param-name>...</param-name>
	            <param-value>...</param-value>
	        </init-param>
	    </servlet>

		<servlet-mapping>
	        <servlet-name>...</servlet-name>
	        <url-pattern>/...</url-pattern>
	    </servlet-mapping>

		<welcome-file-list>
	        <welcome-file>index.html</welcome-file>
	        <welcome-file>index.xhtml</welcome-file>
	        <welcome-file>index.htm</welcome-file>
	        <welcome-file>index.jsp</welcome-file>
    	</welcome-file-list>

	</web-app>

## JSP（Java Server Page） ##
### JSP本质上是一个Servlet ###
JSP本质是一个Servlet，一个jsp页面在Web应用中会被Servlet容器生成一个对应的Servlet。如对于Web应用（WebDemo）中的一个index.jsp文件，将此WebDemo部署到Tomcat服务器，并启动Tomcat后，会在work\Catalina\localhost\WebDemo\org\apache\jsp目录下生成一个index_jsp.class和一个index_jsp.java文件。index_jsp.java文件的部分内容如下：

	package org.apache.jsp;

	import javax.servlet.*;
	import javax.servlet.http.*;
	import javax.servlet.jsp.*;
	import java.util.*;
	
	public final class index_jsp extends org.apache.jasper.runtime.HttpJspBase
	    implements org.apache.jasper.runtime.JspSourceDependent {

		public void _jspInit() {...}

		public void _jspDestroy() {}

		public void _jspService(final javax.servlet.http.HttpServletRequest request, 
			final javax.servlet.http.HttpServletResponse response)
	        throws java.io.IOException, javax.servlet.ServletException {
			
			final javax.servlet.jsp.PageContext pageContext;
		    javax.servlet.http.HttpSession session = null;
		    final javax.servlet.ServletContext application;
		    final javax.servlet.ServletConfig config;
		    javax.servlet.jsp.JspWriter out = null;
		    final java.lang.Object page = this;
			javax.servlet.jsp.JspWriter _jspx_out = null;
	    	javax.servlet.jsp.PageContext _jspx_page_context = null;
	
	
		    try {
				response.setContentType("text/html;charset=ISO-8859-1");
				pageContext = _jspxFactory.getPageContext(this, request, response,
							null, true, 8192, true);
				_jspx_page_context = pageContext;
				application = pageContext.getServletContext();
				config = pageContext.getServletConfig();
				session = pageContext.getSession();
				out = pageContext.getOut();
				_jspx_out = out;
				
				out.write('\r');
				out.write('\n');
				out.write("<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.01 Transitional//EN\">\r\n");
				out.write("<html>\r\n");
				...
				out.write("\r\n");
				out.write("</html>\r\n");
		    } catch (java.lang.Throwable t) {
		      if (!(t instanceof javax.servlet.jsp.SkipPageException)){
		        out = _jspx_out;
		        if (out != null && out.getBufferSize() != 0)
		          try {
		            if (response.isCommitted()) {
		              out.flush();
		            } else {
		              out.clearBuffer();
		            }
		          } catch (java.io.IOException e) {}
		        if (_jspx_page_context != null) _jspx_page_context.handlePageException(t);
		        else throw new ServletException(t);
		      }
		    } finally {
		      _jspxFactory.releasePageContext(_jspx_page_context);
		    }
		}
	}
如上，HttpJspBase继承自HttpServlet，所以JSP页面本质上是一个Servlet。
从jsp页面文件生成的java源文件中，我们还可以看到JSP的8种内置对象其实就是在_jspService方法中声明的局部变量（另外还有一个exception对象只有在jsp错误页面才会有声明）。并且JSP页面中的静态HTML内容其实都是在_jspService方法中通过内置对象out，以字符串的形式输出到客户端浏览器。

### .jsp文件的组成 ###
一个jsp文件由: JSP注释、JSP声明、JSP输出表达式、JSP脚本、JSP编译指令、JSP动作指令、JSP自定义标签、JSP2提供的EL表达式，以及静态HTML内容组成。另外，jsp文件中还可以使用JSP提供的9个内置对象的API。
如下是一个简单的jsp文件：

	<%@ page contentType="text/html; charset=GBK" language="java" errorPage="" %>

	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

	<html>
	<head>
		<title>Insert title here</title>
	</head>
	<body>
		这是一个JSP页面， 当前时间是：
		<%out.println(new java.util.Date()); %>
	</body>
	</html>

### JSP的4中基本语法 ###	
#### JSP注释 ####
JSP注释格式如下：

	<%-- 注释内容 --%>

JSP注释不会发送到客户端，所以在浏览器上查看源代码时无法看到JSP注释（HTML的注释可以看到）

#### JSP声明 ####
JSP声明格式如下：

	<%! 声明部分 %>

JSP声明用于声明用于声明变量和方法，声明的变量和方法作为JSP页面对应生成的Servlet的成员变量和成员方法。

	<%! private int count; %>
	<%! private void method(){
		...
	} %>

JSP声明方法时不能使用abstract修饰，因为抽象方法将导致jsp对应的Servlet变成抽象类，从而无法实例化。

每个客户端浏览器访问Web应用时都会开启一个线程，多个客户端浏览器访问同一个jsp页面，jsp页面只有第一次被访问时才会生成一个对应的Servlet实例，就相当于一个Servlet实例会在多个线程中调用到，因此对于JSP页面声明的变量作为Servlet实例的成员变量，被多个线程访问操作时，会存在线程安全问题。

#### JSP输出表达式 ####
JSP输出表达式的格式如下：

	<%=表达式 %>   注意：输出表达式后面不能加分号

输出表达式 <%=...%> 的本质就是 out.write(...);

#### JSP 脚本 ####
JSP脚本的格式如下：

	<% Java代码 %>

JSP脚本中的Java代码都会转成对应Servlet中_jspService方法中的代码，所以_jspService方法中允许的Java代码（如声明局部变量，调jsp内置对象的API）都可以写在JSP脚本中。需要指出的是，JSP脚本中声明的局部变量不能用private，public等访问控制符修饰，也不能用static修饰。

### JSP的3个编译指令 ###
JSP编译指令就是通知JSP引擎的消息，不直接生成输出。另外编译指令都有默认值。编译指令在将JSP编译成Servlet时起作用。
常见的编译指令有三个：page、include、taglib。
使用编译指令的语法格式如下：
	
	<%@ 编译指令名 属性名="属性值" 属性名="属性值" ... %>   属性键对值之间空格隔开

#### page指令 ####
page指令通常位于JSP页面的顶端，一个JSP页面可以有多条page指令。

page部分属性介绍如下：

	language： 声明当前JSP页面使用的脚本语言，默认是java

	contentType： 设置当前JSP页面输出内容生成的网页的文件格式（MIME类型），以及此文件的编码字符集。
		默认的MIME类型是text/html；默认的字符类型是ISO-8859-1。

	pageEncoding： 指定生成的网页编码字符集。跟contentType中指定的是同一网页文件。

	extends： 指定JSP页面编译所产生的Java类所继承的父类，或所实现的接口

	import： 在JSP页面导入包。默认导入的包有： java.lang.*、javax.servlet.*、javax.servlet.jsp.*、javax.servlet.http.* 

	session： 设定当前JSP页面是否需要HTTP Session。

	buffer： 指定输出缓冲区out的大小。out是JSP的内置对象，可缓存JSP页面对客户浏览器的输出，默认是8KB，可以设置none或其他值，单位KB。
	
	autoFlush： 当输出缓冲区即将溢出时，是否需要强制输出缓冲区的内容。true表示强制输出，false则会在buffer溢出时产生一个异常。

	info： 配置当前JSP页面的说明信息，可通过Servlet的getServletInfo方法获取属性值。

	errorPage： 指定处理错误的JSP页面。如果当前JSP页面产生异常或错误，并且在当前JSP页面没有对应的处理代码，则会跳转到errorPage指定的JSP页面做错误处理。对于IE浏览器，需要把 工具>选项>Internet选项>高级>显示友好HTTP错误信息 取消掉，才能显示errorPage设置的错误处理页面。

	isErrorPage： 设置当前JSP页面为处理错误的页面，只有在isErrorPage设置为true的错误处理页面才会
		生成JSP内置对象exception。如果一个页面产生了异常错误，并且在该页面做了错误处理，则此页面不
		需要指定isErrorPage为true。

#### include指令 ####
静态include，把include指定的jsp文件完全包含进当前文件，融合成一个页面。
静态include在编译时就会把被指定的被包含页面写入到当前JSP页面对应的Servlet中。
被包含的jsp页面不需要是一个完整的页面。
被包含的jsp页面中的编译指令也会被包含进来，如果两个jsp页面的编译指令有冲突，则会报错。

include编译指令语法如下：

	<%@ include file="relativeURLSpec(jsp文件的相对路径)" %>

如果需要经常改变被包含的页面，可以使用JSP提供的动作指令<jsp:include>，此动作指令也称为动态include，动态include不会把被包含的jsp页面中的编译指令包含进来。

### JSP的7个动作指令 ###
动作指令通常可替换成JSP脚本，它只是JSP脚本的标准化写法。

JSP的动作指令主要有如下7个：

	jsp:include 动态包含一个JSP页面

	jsp:forward  执行页面转向

	jsp:param  用于传递参数，必须跟其他支持参数的标签一起使用

	jsp:plugin  用于下载JavaBean或Applet到客户端执行

	jsp:useBean  创建一个JavaBean实例

	jsp:setProperty 设置JavaBean实例的属性值
	jsp:getProperty 获取JavaBean实例的属性值

#### include指令 ####
动态include，包含页面时，不会导入被包含页面的编译指令。
include动作指令格式如下：

	<jsp:include page="{relativeURL|<%=expression>}" flush="true" />
还可以在被导入页面中添加参数

	<jsp:include page="{relativeURL|<%=expression>}" flush="true" >
		<jsp:param name="paramName" value="paramValue" />
	</jsp:include>

其中添加的参数可以在被导入页面中通过request.getParameter("paramName")获取。
其中flush属性为true时表示输出缓存转移到被导入文件中，为false则不转移。默认为false。

动态include导入页面时，在编译生成Servlet中转化成JspRuntimeLibrary.include(ServletRequest
 request, ServletResponse response, String relativePath, JspWriter out, boolean flush)方法，如下

	<jsp:include page="scriptelet.jsp">
Servlet中对应为：

	org.apache.jasper.runtime.JspRuntimeLibrary.include(request, response, 
		"scriptlet.jsp", out, false);

#### forward指令 ####
forward指令用于将页面响应转发到另外的页面。
既可以转发到静态的HTML页面，也可以转发到JSP页面，还可以转发到容器中的Servlet。

forward指令格式如下：

	<jsp:forward page="{relativeURL|<%=expression%>}" />
转发时还可以向目标页面添加参数

	<jsp:forward page="{relative|<%=expression>}">
		<jsp:param name="paramName" value="paramValue" />
	</jsp:forward>
参数通过request.getParameter("paramName")获取。


forward指令转发请求时，虽然浏览器的页面有跳转动作，但浏览器地址栏的url不会改变。forward指令转发请求，只是采用另一个页面来对客户浏览器生成响应。请求参数，请求属性都不会丢失。

forward动作指令在JSP转成Servlet时，使用PageContext.forward(String relativeUrlPath)方法进行处理。而PageContext.forward方法最终是通过RequestDispatcher.forward方法实现的。

#### useBean、setProperty、getProperty动作指令 ####
useBean用于在JSP页面中初始化一个JavaBean实例，
setProperty指令用于给JavaBean实例的属性设置值；
getProperty指令用于获取JavaBean实例的属性值，并输出。

useBean动作指令的格式如下：

	<jsp:useBean id="beanName" class="classname" scope="page|request|session|application"/>
其中id为JavaBean实例名，class为JavaBean类。scope指定JavaBean实例的作用范围。

setProperty动作指令格式如下：

	<jsp:setProperty name="beanName" property="propertyName" value="propertyValue"/>
其中beanName为useBean指令中指定的JavaBean实例名。

getProperty动作指令如下：

	<jsp:getProperty name="beaName" property="propertyName"/>

#### plugin动作指令 ####
plugin指令主要用于下载服务器的JavaBean或Applet到客户端执行。


#### param动作指令 ####
param指令不能单独使用，可以与以下三个动作指令结合使用：

	jsp:include  param指令中的参数传入被导入的JSP页面
	jsp:forward  param指令中的参数传入被转向的页面
	jsp:plugin  param指令中的参数传入JavaBean实例或Applet实例

param指令的格式如下：

	<jsp:param name="paramName" value="paramValue"/>


### JSP脚本中的9个内置对象 ###
在JSP页面对应的Servlet的_jspService方法中会创建这些内置对象（exception实例只有当JSP页面的page编译指令中的isErrorPage设为true时才会创建，默认为false，所以默认不创建exception实例）

JSP脚本中的9个内置对象如下：

	application： javax.servlet.ServletContext的实例。表示当前Web应用的上下文

	config： javax.servlet.ServletConfig的实例。代表JSP/Servlet的配置信息。

	exception： java.lang.Throwable的实例。只有当前页面page编译指令中的isErrorPage设为true时才会创建该实例，代表其他JSP页面中的异常错误

	out：javax.servlet.jsp.JspWriter的实例。代表JSP页面的输出流，用于输出内容，形成HTML页面。

	page： 用java.lang.Object对象表示，代表JSP页面本身（也就是Servlet中的this），所以实例类型就是生成的Servlet子类。

	pageContenxt： javax.servlet.jsp.PageContext的实例。代表JSP页面的上下文

	request： javax.servlet.http.HttpServletRequest的实例。代表一次请求的封装，客户端一次请求中的请求参数都被封装在此对象中。

	response： javax.servlet.http.HttpServletResponse的实例。代表服务器对客户端的响应。对于输出HTML文本内容给客户端时，可使用out对象发出对客户端的响应。对于要输出非字符响应给客户端时（如输出一张图片，则要使用response对象）。另外response对象还可用于重定向。

	session： javax.servlet.http.HttpSession的实例。代表一次会话，当客户端浏览器与站点建立连接时，会话开始；当客户端关闭浏览器时，会话结束。

#### JSP、Servlet之间的数据共享 ####
JSP页面、Servlet等程序都是由Web服务器（如Tomcat）来调用的。JSP、Servlet之间通常不会相互调用，于是
就产生了一个问题：JSP、Servlet之间如何共享数据？

为了解决这个问题，几乎所有的Web服务器都会提供4个类似的Map结构用于放置需要共享的数据。这4个Map结构的数据存储区分别是：application、session、request、page。它们的区别是数据的共享范围（作用范围）不同：

	application： 对整个Web应用有效，Web应用下的所有JSP、Servlet都能访问。

	session： 数据只在一次会话中有效。即本次会话中的JSP、Servlet都能访问session中的数据。

	request： 数据只在一次请求中有效。即本次请求中的JSP、Servlet都能访问request中的数据。

	page： 数据只在当前JSP页面中有效。即只有在当前的JSP页面中才能访问到page中的数据。

JSP通过application、session、request、pageContext这4个内置对象分别操作application、session、request、page这4个范围中的数据。这4个内置对象都提供如下方法操作数据：

	public void setAttribute(String name, Object value);
	public Object getAttribute(String name);
	public void removeAttribute(String name);

#### 内置对象 application ####
application对象是javax.servlet.ServletContext的实例，既可用于Web应用之间的数据共享，还可用来访问
web.xml中为Web应用配置的参数。

web.xml中为Web应用配置参数的方式如下：

	<web-app ...>
		<context-param>
			<param-name>driver</param-name>
			<param-value>com.mysql.jdbc.Driver</param-value>
		</context-param>
		
		...
		
	</web-app>
context-param标签配置的参数对整个Web应用有效，所以也被称为Web应用的配置参数。
通过application对象提供的如下方法可访问Web应用的配置参数：

	public boolean setInitParameter(String name, String value);
	public String getInitParameter(String name);
	public Enumeration<String> getInitParameterNames();

	如： <% application.getInitParameter("driver"); %>

#### 内置对象 config ####
config对象是javax.servlet.ServletConfig的实例。可用于获取JSP、Servlet的配置信息。
因为JSP页面通常不需要配置，所以config一般在Servlet中使用（Servlet需要注册配置后才能使用）。

虽然通常JSP页面不需要配置，但实际上也可以在web.xml中配置JSP，web.xml中把JSP当成Servlet配置：

	<web-app ...>
		<servlet>
			<servlet-name>config</servlet-name>
			<jsp-file>/configTest2.jsp</jsp-file>
			<init-param>
				<param-name>name</param-name>
				<param-value>crazyit.org</param-value>
			</init-param>
		</servlet>
		<servlet-mapping>
			<servlet-name>config</servlet-name>
			<url-pattern>/config</url-pattern>
		</servlet-mapping>
	</web-app>

配置JSP跟配置Servlet的区别就是使用jsp-file标签引入jsp文件。于是可以使用config对象的如下方法获取servlet-name标签配置的名字，以及init-param标签配置的参数信息：

	public String getServletName();
	
	public String getInitParameter(String name);
	public Enumeration<String> getInitParameterNames();

特别的是，如果需要通过config在JSP页面访问web.xml中为此页面配置的参数，那么需要客户端浏览器使用url-pattern标签配置的jsp文件路径来访问此jsp文件，否则获取不到配置参数。


#### 内置对象 exception ####
exception对象只有在异常处理页面中才会被创建（page编译指令的isErrorPage为true）。

异常处理页面就是把JSP页面中的page编译指令中的isErrorPage属性设为true。

使用page编译指令中的errorPage属性可以指定异常处理页面

JSP页面生成的Servlet如下：

	public void _jspService(...) {
		...
	    try {
			...
			out.write("<html>\r\n");
			...
			out.write("</html>\r\n");
	    } catch (java.lang.Throwable t) {
			...
			_jspx_page_context.handlePageException(t);
			...
	    } finally {
	     	...
	    }
	}

如上可知，_jspService方法中已经对所有异常做了try-catch处理，所以JSP页面中的JSP脚本，JSP输出表达式都不需要再处理异常了，但是使用JSP声明语法声明一个方法时，需要在方法中处理异常，因为此方法中的Java代码不在_jspService方法中的try-catch块内。

_jspx_page_context.handlePageException方法中处理异常时，会判断如果在当前JSP页面的page编译指令中为errorPage属性指定了异常处理页面，那么就本次请求forward转向到指定的异常错误页面，否则就使用系统页面来输出异常信息。

在JSP异常处理机制中，多个JSP页面中的page编译指令中的errorPage属性可以指向同一个异常处理页面。也就是说一个异常处理页面可以处理多个JSP页面产生的异常错误。

#### 内置对象 out ####
out对象是javax.servlet.jsp.JspWriter的实例。代表一个页面输出流。
输出表达式 <%=...%> 的本质就是 out.write(...);

#### 内置对象 pageContext ####
pageContext对象是javax.servlet.jsp.PageContext的实例。代表JSP页面的上下文。

pageContext对象除了提供操作page范围内的共享数据外，还提供了如下方法用来操作所有范围内（application、session、request、page）的共享数据：

	public abstract void setAttribute(String name, Object value, int scope);
	public abstract Object getAttribute(String name, int scope);
	public abstract void removeAttribute(String name, int scope);
	
	public abstract int getAttributesScope(String name);  返回指定属性name所在的范围

	public abstract Enumeration<String> getAttributeNamesInScope(int scope);

	public abstract Object findAttribute(String name);  按顺序：page、request、session、	application查找属性name的值。

	public abstract void removeAttribute(String name);  把所有范围内的name属性删除掉。

PageContext中为表示page、request、session、application这4个范围，定义了如下4个常量值：

	public static final int PAGE_SCOPE = 1;
	public static final int REQUEST_SCOPE = 2;
	public static final int SESSION_SCOPE = 3;
	public static final int APPLICATION_SCOPE = 4;

此外，通过pageContext对象还可获取到其他的8个内置对象：

	public abstract ServletContext getServletContext();

	public abstract ServletConfig getServletConfig();

	public abstract ServletRequest getRequest();

	public abstract ServletResponse getResponse();

	public abstract HttpSession getSession();

	public abstract Object getPage();

	public abstract Exception getException();

	public abstract JspWriter getOut();

#### 内置对象 request ####
request对象是javax.servlet.http.HttpServletRequest的实例。
每个request对象封装着一次用户请求，所有的请求头和请求参数都被封装在request对象中。
服务器端负责解析请求头、请求参数的就是JSP或Servlet。而JSP、Servlet获取请求头和请求参数的途径就是request。

request提供如下方法获取请求头：

	public String getHeader(String name);
	public Enumeration<String> getHeaders(String name);  获取请求头的多个值。
	public Enumeration<String> getHeaderNames();

request提供如下方法获取请求参数：

	public String getParameter(String name);
	public String[] getParameterValues(String name); 当请求参数有多个值时，返回多个值组成的数组
	public Enumeration<String> getParameterNames();
	public Map<String, String[]> getParameterMap();

客户端发送请求参数通常有2种方式，GET方式和POST方式 ：

GET方式的请求： 

	直接在浏览器地址栏输入地址，按回车时发送的是GET请求；
	提交表单数据时，没有为form标签的method属性设置值，或设置为get时，发送的是GET请求。

	GET请求会将请求参数键值对附加在原URL地址后，如：url?param1=value1&param2=value2&...
	所以在地址栏上可看到GET方式发送的请求参数。

	GET方式发送的请求参数包含非西欧字符时，Web应用中的JSP/Servlet收到的是非西欧字符的url编码形式，
	如果要转成正常的非西欧字符显示，需要在显示之前通过UrlDecoder.decode(String s, String enc)进行解码，其中解码时采用的字符集enc由浏览器决定，因为是url对非西欧字符进行的url编码，一般要么采用UTF-8字符集，要么是GBK字符集。

	GET请求传送的数据一般不能大于2KB。

POST方式的请求：

	通常是使用表单的方式来发送POST请求，并且form标签的method属性要设置为post。

	POST方式发送的请求参数会放在HTML HEADER中传输，用户不能在地址栏看到。

	如果POST发送的请求参数包含非西欧字符，则在调request.getParameter获取请求参数之前，要先调用request.setCharacterEncoding方法设置编码的字符集。此处设置的字符集就是表单JSP页面所采用的字符集

	通常认为POST请求传送的数据的大小不受限制，但往往取决于服务器的限制。


request对象还可用于操作request范围内的数据


request对象提供一个getRequestDispatcher(String path)方法获取RequestDispatcher实例，通过RequestDispatcher实例可实现JSP动作指令forward的转向功能和动作指令include的动态包含功能。
	
	ServletRequest
		public RequestDispatcher getRequestDispatcher(String path);

	RequestDispatcher
		public void forward(ServletRequest request, ServletResponse response)
		public void include(ServletRequest request, ServletResponse response)

其中path就是希望forwar或include的目标JSP页面文件的路径或Servlet的url-pattern。并且path字符串必须以斜线"/"开头。











## 上传文件 ##
表单中form标签需要指定enctype="multipart/form-data"，表示该表单可用于上传文件
处理上传文件的表单的Servlet需做如下配置：

	使用@MultipartConfig注解修饰Servlet类
	
		@WebServlet(name="Upload", urlPatterns={"/upload"})
		@MultipartConfig
		public class UploadServlet extends HttpServlet

	或者在web.xml文件中配置：

		<servlet>
			<servlet-name>Uplaod</servlet-name>
			<servlet-class>UploadServlet</servlet-class>
			<multipart-config/>
	    </servlet>
		<servlet-mapping>
	        <servlet-name>Upload</servlet-name>
	        <url-pattern>/upload</url-pattern>
	    </servlet-mapping>

在Servlet中获取上传的文件，并操作文件信息，如获取文件大小，文件类型，文件名，将文件保存到硬盘等操作。

	//uploadfile对应表单中的文件上传域中的name属性值
	Part part = request..getPart("uploadfile"); 

	//获取上传文件的类型
	String type = part.getContentType();

	//获取上传文件的大小
	long size = part.getSize();

	//遍历文件上传域的Header name
	Collection<String> headerNames = part.getHeadersNames();
	for (String headerName : headerNames) {
		String header = part.getHeader(headerName);
	}

	//获取包含原始文件名的字符串：
	String fileNameInfo = part.getHeader("content-disposition");

	//提取上传文件的原始文件名
	String fileName = fileNameInfo.substring(fileNameInfo.indexOf("fileName=\") + 10, 
			fileNameInfo.length() - 1);

	//将上传的文件写入服务器硬盘。保存到Web应用根路径下的uploadFiles目录中。需要创建此目录
	part.write(getServletContext.getRealPath("/uploadFiles") + "/" + fileName);

### 使用common-uploadfile开源框架上传文件 ###

## 下载文件 ##
设置响应头，告诉浏览器以下载文件的方式打开要访问的数据

	content-diposition:attachment;filename=1.txt

将要下载的文件当做一个流与response.getOutputStream关联起来

	InputStream in = new FileInputStream("D:\\aa\\bb\1.txt");
	OutputStream out = response..getOutputStream();
	...IO对拷...


	
	

	
	

	


	