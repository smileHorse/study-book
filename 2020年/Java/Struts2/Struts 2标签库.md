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

