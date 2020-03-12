# 使用JSP标准标记库JSTL（JSP Standard Tag Library）

## 1 JSTL的使用方法

- 将`jstl.jar`和`standard.jar`拷贝到项目所在的`WEB-INF\lib`目录；
- 解压`standard.jar`，将里面的`c.tld`文件拷贝到`WEB-INF`目录；
- 在使用JSTL的JSP文件中加入指令：

~~~jsp
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
~~~

##  2 `<c:forEach>`实现循环,迭代处理数组和集合

~~~jsp
<table>
    <c:forEach var="movie" items="${movieList}" varStatus=movieLoopCount>
        <tr>
        	<td>Count: ${movieLoopCount.count}</td>
        </tr>
        <tr>
            <td>${movie}</td>
        </tr>
    </c:forEach>
</table>
~~~

这个标记将集合中的每个元素赋给用var属性声明的那个变量。可选的`varStatus`属性得到一个循环计数器，它有一个count属性，可以提供迭代计数器的当前值。

嵌套的`<c:forEach>`：

~~~jsp
<c:forEach var="listElement" items="${movies}">
    <c:forEach var="movie" items="${listElement}">        
        <tr>
            <td>${movie}</td>
        </tr>
    </c:forEach>
</c:forEach>
~~~

`<c:forEach>`标记还有可选的begin和end属性，用于对集合的一个子集进行迭代处理。还提供了step属性跳过某些元素。

**`<c:forEach>`中的`c`是一个必要的前缀。所有标记和EL函数都必须有一个前缀，它会为容器指定该标记或函数名的命名空间。不过`c`只是一个标准约定，用来表示JSTL中所谓的`core`标记。**

## 3 `<c:if>`完成条件包含

~~~jsp
<c:if test="${userType eq 'member'}">
	<jsp:include page="inputComments.jsp"></jsp:include>
</c:if>
~~~

`<c:if>`没有else子句，不能用于条件判断。

### 4 `<c:choose>和<c:when>、<c:otherwise>`

~~~jsp
<c:choose>
	<c:when test="${userPref eq 'performance'}">
        ...
    </c:when>
	<c:when test="${userPref eq 'safety'}">
        ...
    </c:when>
    <c:otherwise>
        ....
    </c:otherwise>
</c:choose>
~~~

最多只能运行这几个体中的一个。`<c:choose>`标记不一定要有`<c:otherwise>`标记。

## 5 `<c:set>`

`<jsp:setProperty>`只能做一件事：设置bean的性质。`<c:set>`提供了两种不同的设置：var和target。

- var： 用于设置属性变量。
- target： 用于设置bean的性质或Map值。

这两种版本都有两种形式：有体或没有体。`<c:set>`体只是放入值的另一种途径。

### 5.1 设置属性变量

没有体：

~~~jsp
<c:set var="userLevel" scope="session" value="Cowboy" />
<c:set var="Fido" value="${person.dog}" />
~~~

有体：

~~~jsp
<c:set var="userLevel" scope="session">
    Sheriff, Bartender, Cowgirl
</c:set>
~~~

上面的示例中，如果没有一个名为"userLevel"的会话作用域属性，这个标记就会创建这样一个属性（假设value属性不为null）。

如果${person.dog}计算为一个Dog对象，那么Fido的类型就是Dog。

- `<c:set>`中不能同时有var和target属性；
- scope是可选的。如果没有使用这个属性,则默认为页面作用域。查找顺序为页面作用域、请求作用域、会话作用域，最后是应用上下文作用域。
- 如果value为null，var指定的属性将被删除；
- 如果var指定的属性不存在，则会创建一个属性。但仅当value不为null时才会创建新属性。如果没有指定scope，默认在页面作用域中创建；

### 5.2 对bean和Map使用`<c:set>`

只能用来设置bean的性质或Map值，不能用来向列表或数组中增加元素。

没有体：

~~~jsp
<c:set target="${PetMap}" property="dogName" value="Clover" />
~~~

有体：

~~~jsp
<c:set target="${person}" property="name">
	${foo.name}
</c:set>
~~~

- 如果target表达式为null，容器会抛出一个异常；
- target用来放一个能解析为实际对象的表达式。如果放入一个String直接量（表示bean或Map的“id”名），这是不行的。换句话说，target并非用来放bean或Map的属性名，而是用于指定具体的属性对象。
- 如果target表达式不是一个Map或bean，容器会抛出一个异常；
- 如果target表达式是一个bean。但是这个bean没有与property匹配的属性,容器就会抛出一个异常。不过要当心，因为如果性质不存在，EL表达式本身不会导致异常。所以，即使${fooBean.notAPropery}本身没有产生异常,它只是返回null。倘若target属性的值是“notAProperty”，容器会抛出一个异常。

## 6 `<c:remove>`删除一个属性

~~~jsp
<c:remove var="userStatus" scope="request"></c:remove>
~~~

var属性必须是一个String直接量，不能是表达式。scope是可选的，默认作用域是页面作用域。

## 7 `<c:import>`包含资源文件

现在有三种方法将一个资源的内容增加到JSP中：

- include指令

~~~jsp
<%@ include file="Header.html" %>
~~~

静态：在转换是将file属性值指定的文件内容增加到当前页面

- `<jsp:include>`标准动作

~~~jsp
<jsp:include page="Header.jsp"></jsp:include>
~~~

动态：在请求时将page属性值指定的文件内容增加到当前页面

- `<c:import>`JSTL标记

~~~jsp
<c:import url="http://www.wickedlysmart.com/skyler/horse.html"></c:import>
~~~

动态：在请求时将url属性值指定的内容增加到当前页面。它与`<jsp:include>`非常相似，但更加强大。url可以在Web容器范围之外。

### 7.1 使用`<c:import>`和`<c:param>`定制所包含的内容

使用`<c:import>`的JSP：

~~~jsp
<html>
    <body>
        <c:import url="Header.jsp">
        	<c:param name="subTitle" value="We take the string out of SOAP."></c:param>
        </c:import>
    </body>
</html>
~~~

所包含的文件"Header.jsp"：

~~~jsp
<img src="images/Web-services.jpg"><br>
<em><strong>${param.subTitle}</strong></em>
~~~

使用`<jsp:include>`也可以实现同样的效果，被包含的文件不用改动：

~~~jsp
<jsp:include url="Header.jsp">
    <jsp:param name="subTitle" value="We take the string out of SOAP."></jsp:param>
</jsp:include>
~~~

## 8 `<c:url>`可以满足所有超链接需求

servlet中的URL重写：

~~~java
public void doGet(HttpServletRequest request, HttpServletResponse response) 
    throws IOException, ServletException() {
    response.setContentType("text/html");
    PrintWriter out = response.getWriter();
    HttpSession session = request.getSession();
    
    out.println("<html><body>");
    out.println("<a href=\"" + response.encodeURL("/BeerTest.do") + "\">click</a>");
    out.println("</body></html>");
}
~~~

使用JSP的URL重写

~~~jsp
<a href="<c:url value='/inputComments.jsp' />">Click here</a>
~~~

### 8.1 URL编码

URL编码需要把不安全/保留的字符替换为其他字符，然后再在服务器端完成解码。

~~~jsp
<c:set var="first" value="Crouching Pixels"></c:set>
<c:set var="last" value="Hidden Cursor"></c:set>
<c:url value="/inputComments.jsp" var="inputURL">
	<c:param name="firstName" value="${first}""></c:param>
    <c:param name="lastName" value="${last}"></c:param>
</c:url>
~~~

`<c:param>`会负责编码。

## 9 `<error-page>`建立自己的错误页面

### 9.1 使用page指令配置错误页面

指定的错误页面errorPage.jsp：

~~~jsp
<%@ page isErrorPage="true" %>
<html>
  ....
</html>
~~~

抛出异常的坏页面badPage.jsp：

~~~jsp
<%@ page errorPage="errorPage.jsp" %>
<html>
    抛出异常
</html>
~~~

请求badPage.jsp时，该页面抛出一个异常，所以响应来自errorPage.jsp。

### 9.2 `<error-page>`标记

可以在DD中为整个WEB应用声明错误页面，甚至可以为不同的异常类型或HTTP错误码类型（404、500等）配置不同的错误页面。

容器使用DD中的`<error-page>`配置作为默认错误页面，但是如果JSP有一个明确的errorPage page指令，容器就会优先使用指令。

在DD中，可以根据`<exception-type>`或HTTP状态码`<error-code>`声明错误页面。采用这种做法，可以根据产生错误的问题类型向客户显示不同的错误页面。`<location>`必须相对于web-app根/上下文，这说明它必须以一个斜线`/`开头。

- 声明一个普遍型错误页面：

它应用于WEB应用的所有部分，不只是JSP。可以在单个的JSP中增加一个包含errorPage属性的page指令覆盖这个设置。

~~~xml
<error-page>
	<exception-type>java.lang.Throwable</exception-type>
    <location>/errorPage.jsp</location>
</error-page>
~~~

- 为更明确的异常声明一个错误页面：

以下配置了一个错误页面，只有存在ArithmeticException异常时才调用这个错误页面。如果既有这个声明，又有以上的普遍型声明，那么只要不是ArithmeticException，其他异常都会导致调用errorPage.jsp。

~~~xml
<error-page>
	<exception-type>java.lang.ArithmeticException</exception-type>
    <location>/arithmeticException.jsp</location>
</error-page>
~~~

- 根据一个HTTP状态码声明错误页面：

以下配置了一个错误页面，只有响应的状态码是404时才调用这个错误页面。

~~~xml
<error-page>
	<error-code>404</error-code>
    <location>/notFoundError.jsp</location>
</error-page>
~~~

### 9.3 exception对象

错误页面实际上就是一个处理异常的JSP，所以容器为这个页面提供了一个额外的exception对象。在scriptlet中，可以使用隐式对象exception，在JSP中，可以使用EL隐式对象${pageContext.exception}。这个对象的类型是java.lang.Throwable。

exception只对有明确定义page指令的错误页面可用。

~~~jsp
<%@ page isErrorPage="true" %>
<html>
    <body>
        You caused a ${pageContext.exception} on the server.
    </body>
</html>
~~~

## 10 `<c:catch>`标记捕获一个异常

把有风险的EL或标记调用包在`<c:catch>`的体中，异常就会在这里捕获。一旦异常发生，控制就会跳至`<c:catch>`的最后。

~~~jsp
<c:catch var="myException">
	Inside the catch...
    <% int x = 10/0; %>
</c:catch>
<c:if test="${myException != null}">
    There was an exception: ${myException.message} <br>
</c:if>
~~~

使用可选的var属性，它会在页面作用域中创建一个新的属性，并把异常对象赋给这个变量。