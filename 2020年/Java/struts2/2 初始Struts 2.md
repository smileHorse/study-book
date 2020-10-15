# 2 初始Struts 2

## 2.1 声明性架构

​	声明性架构是一种特殊的配置方式，它允许开发人员以描述而不是硬编码干预的方式创建应用程序的架构。开发人员使用高级工件artifact来描述架构组件，例如XML文件或者java注解，系统会利用这些高级工件创建应用程序的运行时实例。

​	声明性架构包括定义应用程序使用的Struts 2组件，以及将它们连接起来形成需要的工作流程路径（workflow path）。这里说的工作流路径是指当一个特定的URL被单击时会触发哪个动作，以及该动作会选择哪个结果来完成处理流程。

​	开发人员只需要声明哪些对象作为应用程序的动作（action）、结果（result）以及拦截器（interceptor）。这个声明过程主要包括指定哪个Java类实现哪个接口。几乎所有的Struts 2架构组件都被定义为接口。实际上，框架提供了你可能会用到的几乎所有组件的实现。例如，框架包含了很多结果的实现以支持不同类型的视图层技术。通常情况下，开发人员只需要实现动作，之后将它们与内建的结果和拦截器关联起来即可。此外，使用智能默认值和注解可以进一步减少配置过程中所需要的手工任务。

### 2.1.1 声明架构的两种方式

​	声明应用程序的架构有两种不同的方式：通过基于XML的配置文件或者通过Java注解。不管使用哪种方式，框架都会产生相同的运行时应用程序，它们没有功能上的优先顺序。在使用XML的情况下，我们使用带有描述应用程序的动作、结果和拦截器等元素的XML配置文档。在使用Java注解的情况下，没有XML文件。所有的元数据都集中放在Java注解中，这些注解直接驻留在实现动作的类对应的Java源代码中。

#### 2.1.1.1 基于XML的声明性架构

​	通常情况下，XML文档中包含了表示应用程序组件的元素。**一个应用程序有几个包含上述元素的XML文件，这些元素描述了应用程序的所有组件。尽管大部分的应用程序都有多个XML文件，但是所有的这些文件就像一个大描述文件那样一起工作。框架使用一个专门的文件作为进入这个大描述文件的入口点，这个入口点文件就是struts.xml文件。这个文件驻留在Java类路径（classpath）下，并且必须由开发人员创建。虽然可以在struts.xml文件中声明所有的组件，但是为了应用程序的模块化，开发人员通常只用这个文件包含次级的XML文件。**

#### 2.1.1.2 基于Java注解的声明性架构

​	注解必须写在那些实现了动作的Java类上。很多人认为相对于XML机制来说，基于注解的机制是一个更优雅的解决方案。最起码，注解机制与基于约定的信息推断紧密结合。换句话说，有些在XML元素中必须显示指定的信息，可以从注解的类所隶属的Java包结构自动推断出来。

### 2.1.2 智能默认值

​	很多常用的Struts 2组件（或者组件的属性）不需要开发人员声明。这些已经被框架声明了，以便开发人员可以更快地实现应用程序功能的常用部分。一些框架组件（如拦截器和结果类型）可能从来都不需要开发人员直接声明，因为系统提供的这些组件能够处理大部分开发人员的日常需求。其他的一些组件（比如动作组件和结果组件）需要开发人员自己声明，但是很多常用属性设置可以继承框架的默认值。

​	这些预定义的组件是Struts 2智能默认值的一部分。struts-default.xml中声明了很多这样的组件，这个文件在struts-core.jar中。



## 2.2 HelloWorld示例

### 2.2.1 程序的基本结构

- struts2inaction

  - src
    - manning
      - chapterTwo
        - chapterTwo.xml
        - HelloWorld.java
    - **struts.xml**
  - web
    - chapterTwo
      - HelloWorld.jsp
      - NameCollector.jsp
    - WEB-INF
      - classes
      - lib
      - **web.xml**
    - index.html
    - index.jsp

  ​	首先，所有的第一层目录（除了WEB-INF以外）都在Web应用程序的文档根目录（web）中。通常情况下，所有JSP文件都放在这些目录中。也可以在这些目录中放置FreeMarker或Velocity的模板文件。当然这些资源也可以从类路径下的JAR文件中加载。

  ​	关于文档根目录需要注意的一项非常重要的事情是，其中的资源可以被Servlet容器潜在地当做静态内容。如果不对这种访问加以保护，一个直接指向文档根目录中资源的URL可能会导致Servlet容器将这个资源送出。因此，文档根目录不是存放敏感信息的安全场所。

  ​	所有重要的内容都放在WEB-INF目录下。该目录应该包含两个目录lib、classes和一个web.xml文件。还有一个src目录，其中包含项目的源代码。但是src目录不是Web应用程序目录结构所必需的一部分。lib和classes目录是必需的。lib目录用来存放所有应用程序需要的JAR文件。classes目录用来存放应用程序需要使用的所有Java类文件。这些内容与lib中的资源没有本质区别，但是classes目录包含展开的目录结构，其中包含class文件而不是JAR文件。web.xml是整个应用程序的中心配置文件。这个文件的正式名称是部署描述符（deployment descriptor），它包含了Web应用程序中所有的Servlet、Servlet过滤器和其他Servlet API组件的定义。

### 2.2.2 HelloWorld程序

​	`web.xml`:

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <display-name>S2 Example Application - Chapter 1 - Hello World</display-name>

    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
        <init-param>
            <param-name>actionPackages</param-name>
            <param-value>manning</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    
    <servlet>
        <servlet-name>anotherServlet</servlet-name>
        <servlet-class>manning.servlet.AnotherServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>anotherServlet</servlet-name>
        <url-pattern>/anotherServlet</url-pattern>
    </servlet-mapping>
    
    <welcome-file-list>
        <welcome-file>index.html</welcome-file>
    </welcome-file-list>
</web-app>
~~~

`struts.xml`:

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <!-- 使用常量调整Struts的属性 -->
    <constant name="struts.devMode" value="true"></constant>
    
    <!-- Menu动作属于默认包 -->
    <package name="default" namespace="/" extends="struts-default">
        <action name="menu">
            <result>/menu/Menu.jsp</result>
        </action>
    </package>
    
    <!-- include元素模块化了XML文档 -->
    <include file="manning/chapterTwo/chapterTwo.xml"></include>
</struts>
~~~

`menu/Menu.jsp`:

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="s" uri="/struts-tags" %>
<html>
<head>
    <title>Struts 2 in Action: Menu</title>
</head>
<body>
    <hr>
    <ul>
        <li><a href="<s:url action='chapterTwo/Name'/>">HelloWorld</a></li>
        <li><a href="<s:url action='chapterTwo/annotatedNameCollector'/>">AnnotatedHelloWorld</a></li>
    </ul>
    <hr>
</body>
</html>

~~~

`manning/chapterTwo/chapterTwo.xml`:

~~~xml
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <package name="chapterTwo" namespace="/chapterTwo" extends="struts-default">
        <action name="Name">
            <result>/chapterTwo/NameCollector.jsp</result>
        </action>
        
        <action name="HelloWorld" class="manning.chapterTwo.HelloWorld">
            <result name="SUCCESS">/chapterTwo/HelloWorld.jsp</result>
        </action>
    </package>
</struts>
~~~

​	这里仅有的元素时struts根元素和package元素。struts元素是所有Struts 2 XML文件的强制性的文档根元素，package元素是一个重要的容器元素，用来组织应用程序的动作元素、结果元素和其他组件元素。每个动作都声明了一些它们使用的结果，每个结果都命名了一个用来呈现结果页面的JSP页面。

​	package元素声明了一个当框架将URL映射到动作时会用到的命名空间：

|  协议   |  主机名:端口   |   Servlet上下文   | 包命名空间  |   动作名.action    |
| :-----: | :------------: | :---------------: | :---------: | :----------------: |
| http:// | localhost:8080 | /manningSampleApp | /chapterTwo | /HelloWorld.action |

​	Name动作没有任何真正的后台处理，它只是转到呈现给用户一个收集名字的表单的页面。即使是一个简单地没有动态处理的JSP页面，也要用空的动作组件来转到你的结果。这可以让应用程序的架构保持一致，可以预先防备工作流预计增加的复杂度，并且可以将资源真正结构隐藏在Struts 2动作的逻辑命名空间之后。

​	虽然从技术上讲可以使用一个URL直接指向一个表单JSP，但是一个广泛接受的最佳实践是，使用动作来转发这些请求。这样转发用的动作不需要指定一个实现类。它们会自动转到自己声明的结果页面。

​	HelloWorld动作指定manning.chapterTwo.HelloWorld作为它的实现类。

`manning/chapterTwo/HelloWorld.java`:

~~~java
package manning.chapterTwo;

public class HelloWorld {

    private static final String GREETING = "Hello ";

    private String name;
    private String customGreeting;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCustomGreeting() {
        return customGreeting;
    }

    public void setCustomGreeting(String customGreeting) {
        this.customGreeting = customGreeting;
    }

    public String execute() {
        setCustomGreeting(GREETING + getName());

        return "SUCCESS";
    }
}
~~~

​	虽然很多Struts 2动作会实现Action接口，但是它们只需要满足一个非正式的契约即可。HelloWorld动作通过提供一个返回字符串的execute()方法来满足这个契约。它不需要真正实现Action接口来非正式的满足这个契约。

`chpaterTwo/NameCollector.jsp`:

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="s" uri="/struts-tags" %>
<html>
<head>
    <title>Name Collector</title>
</head>
<body>
    <hr>
    <h4>Enter your name so that we can customize a greeting just for You!</h4>
    <s:form action="HelloWorld">
        <s:textfield name="name" label="Your name" />
        <s:submit />
    </s:form>
    <hr>
</body>

~~~

`chapterTwo/HelloWorld.jsp`:

~~~jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="s" uri="/struts-tags" %>
<html>
<head>
    <title>HelloWorld</title>
</head>
<body>
    <hr>
    <h3>Custom Greeting Page</h3>
    <h4><s:property value="customGreeting"></s:property></h4>
    <hr>
</body>
</html>
~~~

