# Struts 2标签库

​	Struts 2把所有标签都定义在一个s标签库（URI为“/struts-tags”的命名空间）里。可以将标签分为如下三类：

- UI标签：主要用于生成HTML元素的标签；（UI标签又可分为两类）：
  - 表单标签：主要用于HTML页面的form元素，以及普通表单元素的标签；
  - 非表单标签：主要用于生成页面上的树、Tab页等标签；
- 非UI标签：主要用于数据访问、逻辑控制等的标签；（非UI标签又可分为两类）：
  - 流程控制标签：主要包含用于实现分支、循环等流程控制的标签；
  - 数据访问标签：主要包含用于输出ValueStack中的值，完成国际化等功能的标签；
- Ajax标签：用于Ajax支持的标签；

## 1 起步

​	Struts 2内建的标签库极大地简化了JSP页面输出逻辑的实现，借助于Struts 2的标签库，完全可以避免在JSP页面中使用Java脚本，提高了表现层组件的可维护性。

### 1.1 使用Struts 2标签库的准备

​	标签库开发包含两个步骤：开发标签处理类和定义标签库定义文件。这两个步骤都有Struts 2框架提供。

​	在JSP页面中通过如下代码来导入Struts 2标签库：

~~~jsp
<%@taglib prefix="s" uri="/struts-tags"%>
~~~

​	使用以"s"为前缀的标签`<s:text ...>`时，该标签需要使用uri为/struts-tags的标签库处理。通过前缀关联，系统知道从Struts 2标签库寻找名为text的标签来处理该标签。

### 1.2 Struts 2中的OGNL

​	在传统的OGNL表达式求值中，系统会假设只有一个“根”对象，但Struts 2的Stack Context需要多个“根”对象，其中ValueStack只是多个“根”对象的其中之一。

#### 1.2.1 使用传统OGNL求值

​	如果系统的Context中包含两个对象：foo对象，它在Context中的名称为foo；bar对象，它在Context中的名称为bar。将foo对象设置成Context的根对象。

~~~jsp
// 返回foo.getBlah()方法的返回值
#foo.blah
// 返回bar.getBlah()方法的返回值
#bar.blah
// 因为foo是根对象，故返回foo.getBlah()方法的返回值
blah
~~~

​	如果需要访问的属性属于根对象，则可以直接访问该属性；否则必须使用一个命名空间，如#bar。

#### 1.2.2 使用Struts 2中的OGNL求值

​	在Struts 2中，系统的ValueStack是OGNL表达式的第一个根对象，如果最近的Action存在，则Action上下文是第二个根对象。

​	Struts 2不只根据表达式从ValueStack中取得对象，还可以直接从对象中获取属性。Struts 2提供了一个特殊的OGNL属性访问器，它可以从上到下自动搜寻栈内的所有实体。直到找到与求值表达式匹配的属性。

​	假设ValueStack中包含两个实例：Animal和Person，都包含name属性。Animal实例还有一个species属性，Person还有一个salary属性。其中Animal实例是栈顶元素，而Person实例在其后面。

~~~jsp
// 返回animal.getSpecies()方法的返回值
species
// 返回person.getSalary()方法的返回值
salary
// 返回animal.getName()的返回值，因为Struts 2实例先找到Animal实例
name

// 获取Person实例的name属性
person.name
~~~

也可以通过索引来访问ValueStack中的对象：

~~~jsp
// 返回animal.getName()的返回值
[0].name
// 返回person.getName()的返回值
[1].name
~~~

​	Struts 2使用标准的Context来进行OGNL表达式求值，OGNL处理的顶级对象是一个Context，这个Context对象就是一个Map类型实例。在该OGNL的Context中，有一个根对象就是OGNL ValueStack。如果需要访问ValueStack里的属性，直接通过如下方式：

~~~jsp
// 取得ValueStack中的bar属性
${bar}
~~~

​	除此之外，Struts 2还提供了了一些命名对象，这些命名对象与根对象无关，它们只是存在于Stack Context之中，访问这些对象时需要使用#前缀来指明。

- paramerters对象：用于访问HTTP请求参数。例如#parameters['foo']或#parameters.foo，用于返回调用HttpServletRequest的getParameter("foo")方法的返回值；
- request对象：用于访问HttpServletRequest的属性。例如#request['foo']或#request.foo，用于返回调用HttpServletRequest的getAttribute("foo")方法的返回值；
- session对象：用于访问HttpSession的属性。例如#session['foo']或#session.foo，用于返回调用HttpSession的getAttribute("foo")方法的返回值；
- application对象：用于访问ServletContext的属性。例如#application['foo']或#application.foo，用于返回调用ServletContext的getAttribute("foo")方法的返回值；
- attr对象：如果可以访问到，则访问PageContext，否则将依次搜索如下对象：HttpServletRequest、HttpSession、ServletContext中的属性。

**注意：当系统创建了Action实例后，该Action实例已经被保存到ValueStack中，故无需书写#即可访问Action属性。**

### 1.3 OGNL中的集合操作

​	直接生成List类型集合的语法为：`{e1, e2, e3, ...}`；

​	直接生成List类型集合的语法为：`#{key1:value1, key2:value2, key3:value3, ...}`；

​	对于集合，OGNL提供了两个元素符：in和not in，其中in判断某个元素是否在指定集合中，not in用于判断某个元素是否不在指定集合中。

~~~jsp
<s:if test="'foo' in {'foo', 'bar'}">包含</s:if>
<s:else></s:else>
<s:if test="'foo' not in {'foo', 'bar'}">不包含</s:if>
<s:else></s:else>
~~~

​	OGNL还允许通过某个规则取得集合的子集。有如下3个操作符：

- ？：取出所有符合选择逻辑的元素；
- ^：取出符合选择逻辑的第一个元素；
- $：取出符合选择逻辑的最后一个元素；

~~~jsp
// 取出person的所有性别为male的relatives集合
person.relatives.{? #this.gender == 'male'}
~~~

### 1.4 Lambda表达式

​	OGNL支持基本的Lambda表达式语法，通过这种表达式语法，可以在OGNL表达式中使用一些简单的函数。

~~~jsp
// 一个斐波那契数列
<s:property value="#fib = :[#this == 0 ? 0 : #this == 1 ? 1 : #fib(#this - 2) + #fib(#this - 1)], #fib(this)"></s:property>
~~~

