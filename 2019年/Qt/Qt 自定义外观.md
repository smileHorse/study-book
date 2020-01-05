# Qt 自定义围观

​	Qt提供了三种方法来重新定义Qt内置窗口部件的外观：

- 子类化个别的窗口部件类，并重新实现它的绘制和鼠标事件处理器。这必须遍历所有的代码和Qt设计师的窗体，把Qt所有相关的类都改成子类，需要大量的工作。
- 子类化`QStyle`或一个预定义的风格，例如`QWindowStyle`。这种方法很好用。Qt本身就是用这种方法为它所支持的不同平台提供基于平台的外观的。
- 使用Qt样式表。

## 1 使用Qt样式表

​	样式表由影响窗口部件绘制的样式规则组成。这些规则是普通文本。由于在运行时会解析样式表，所以可以通过制定样式表的方式来设计不同的Qt应用程序：使用`-stylesheet file.qss`命令行参数、使用Qt设计师的样式表编辑器，或者在开发应用程序时嵌入一个`QTextEdit`。

​	样式表作用于上层的当前激活的`QStyle`上。因为创建样式表不引入任何子类，所以它们适合对现有窗口部件做微小的定制。

​	对于定制窗口部件的背景颜色，使用样式表比修改窗口部件的调色板更可靠一些。这是因为`QPalette`中的实体（Base、Button、Highlight等等）在不同样式中的用法是不一样的。例如`QWindowStyle`使用Base调色板实体填充只读组合框的背景，然而，`QPlastqueStyle`使用Button实体完成这一功能。此外，有些样式使用硬编码的图像呈现某个元素，从而跳过了调色板。相反，样式表保证了无论激活的是哪种样式，都可以使用指定的颜色。

### 在程序中设置样式表的方式

- `QApplication::setStyleSheet`为整个应用程序设置一个样式表；

  ~~~c++
  qApp->setStyleSheet("QLineEdit { background-color: yellow; }");
  ~~~

- `QWidget::setStyleSheet`设置窗口部件及其子窗口部件的样式表；

  ~~~c++
  dialog->setStyleSheet("QLineEdit { background-color: yellow; }");
  ~~~

- 直接在类上设置样式表，可以忽略类选择器以及大括号；

  ~~~c++
  lineEdit->setStyleSheet("background-color: yellow;");
  ~~~

### 样式表的规则

​	样式规则通常是组合的，例如：

~~~qss
QCheckBox, QComboBox, QLineEdit, QListView, QRadioButton, QSpinBox {
	color: #050505;
	background-color: yellow;
}
~~~

### 样式表属性

#### color和background-color：指定前景色和背景色

颜色的指定方式：

- 名称：可以使用任何能被`QColor::setNameColor()`函数识别的名字。`color: yellow;`
- `#RRGGBB`格式：HTML样式的字符串。`color: #050505;`
- `RGB`或`RGBA`值：指定红、绿、蓝部分，它们的取值范围是0至255，或0%至100%区间的值。`RGBA`额外需要一个透明度作为第四个值。`color: rgb(0, 88, 152);`或`color: rgba(97%, 80%, 9%, 50%)`
- 调色板实体：`color: palette(Base);`
- 渐变：`color: qlineargradient(x1: 0, y1: 0, x2: 1, y2: 1, stop: 0 white, stop: 0.4 gray, stop: 1 green);`

#### background-image：指定一个图片作为背景

~~~qss
QLineEdit {
	background-image: url(:/images/yellow-bg.png);
}
~~~

​	默认情况下，背景图片从窗口部件的左上角(不包含使用margin指定的边缘区域)开始，并且向水平和竖直方向重复填充整个窗口部件，可以通过使用background-position和background-repeat属性进行设置，例如：

~~~qss
QLineEdit {
	background-image: url(:/images/yellow-bg.png);
	background-position: top right;
	background-repeat: repeat-y;
}
~~~

如果指定了背景图片和背景颜色，背景颜色将会在背景图片的半透明区域中透射出来。

#### border-image： 边界图

与使用background-image定义的背景图像不同，边界图被分为3 x 3的小格。当填充窗口部件背景时，4个角(A、C、G、I)保持不变，其他的5个格子被拉伸或者平铺，填充可用空间。

|  A   |  B   |  C   |
| :--: | :--: | :--: |
|  D   |  E   |  F   |
|  G   |  H   |  I   |

使用border-image属性可以指定各个边界图，它要求指定一个图像文件名和定义9个格子的4条切线。切线用其到上、右、下、左边缘的距离定义。例如：

~~~
border-image: url(border.png) 4 8 12 16;
~~~

当使用边界图时，必须显示地设置border-width属性。一般情况下，border-width应该与切线的位置一致；否则，为了与border-width相符合，角上的格子将被拉伸或缩短。对于上面的例子，这样指定边框宽度：

~~~
border-width: 4px 8px 12px 16px
~~~



### 样式表选择器

样式表选择器：

| 选择器   | 实例            | 可以匹配的窗口部件           |
| -------- | --------------- | ---------------------------- |
| 全局对象 | *               | 任意窗口部件                 |
| 类型     | `QDial`         | 给定类的实例，包含子类       |
| 类       | `.QDial`        | 给定类的实例，不包含子类     |
| 标识     | `QDial#ageDial` | 给定对象名称的窗口部件       |
| Qt属性   | `QDial[y="0"]`  | 为某些属性赋值的窗口部件     |
| 子对象   | `QFrame>QDial`  | 给定窗口部件的直接子窗口部件 |
| 子孙对象 | `QFrame QDial`  | 给定窗口部件的子孙窗口部件   |

选择器语法对任何的Qt属性都适用。但样式表不会注意到属性值的修改。选择器也能以各种方式组合。例如，为了选择所有称作`okButton`的`QPushButton`，它们的x和y属性为0，名为frame的`QFrame`直接子对象，可以这样写：

~~~qss
QFrame#frame > QPushButton[x="0"][y="0"]#okButton {...}
~~~

从`Qt4.2`开始，可以使用`QObject::setProperty()`动态创建一个不存在的属性。例如：

~~~c++
nameLineEdit->setProperty("mandatoryField", true);
~~~

辅助控制器，由双冒号指定，可以跟窗口部件一起使用，常见的辅助控制器如下：

| 辅助控制器       | 说明                                           |
| ---------------- | ---------------------------------------------- |
| ::indicator      | 复选框、单选钮、可选菜单项或可选群组框的指示器 |
| ::menu-indicator | 按钮的菜单指示器                               |
| ::item           | 菜单、菜单栏或状态栏项                         |
| ::up-button      | 微调框或滚动条的向上按钮                       |
| ::down-button    | 微调框或滚动条的向下按钮                       |
| ::up-arrow       | 微调框、滚动条或标题视图的向上箭头             |
| ::down-arrow     | 微调框、滚动条、标题视图或组合框的向下箭头     |
| ::drop-down      | 组合框的下拉箭头                               |
| ::title          | 群组框的标题                                   |

增加复选框和单选钮的状态指示器的大小为20 x 20像素，并且确保指示器和说明文字之间有8像素的间隙：

~~~qss
QCheckBox::indicator, QRadioButton::indicator {
	width: 20px;
	heigth: 20px;
}
QCheckBox, QRadioButton {
	spacing: 8px;
}
~~~

样式表状态，由单冒号决定，常见的状态如下：

| 状态           | 说明                       |
| -------------- | -------------------------- |
| :disabled      | 禁用窗口部件               |
| :enabled       | 启用窗口部件               |
| :focus         | 窗口部件具有输入焦点       |
| :hover         | 鼠标在窗口部件悬停         |
| :pressed       | 鼠标按键点击窗口部件       |
| :checked       | 按钮已被选中               |
| :unchecked     | 按钮未被选中               |
| :indeterminate | 按钮被部分选中             |
| :open          | 窗口部件位于打开或扩展状态 |
| :closed        | 窗口部件位于关闭或销毁状态 |
| :on            | 窗口部件的状态是on         |
| :off           | 窗口部件的状态是off        |

将状态一个接一个列出来，并用冒号隔开，可以保证满足所有状态时，规则才能使用。例如只有当鼠标在一个被选中的复选框上悬停时规则才能被使用：

~~~qss
QCheckBox:checked:hover { color: white; }
~~~

如果希望在任何一个状态为true的情况下使用规则，则可以使用多个选择器，用逗号隔开：

~~~qss
QCheckBox:hover, QCheckBox:checked { color: white; }
~~~

逻辑否可以用感叹号表示：

~~~qss
QCheckBox:!checked { color: blue; }
~~~

状态与辅助控制器合用：

~~~qss
QComboBox::drop-down:hover { image: url(:/images/downarrow_bright.png); }
~~~

**当一个属性被具有同一选择器的几个规则同时设置时，只有最后一个规则起作用。**

## 2 子类化`QStyle`

​	`QStyle`类提供了一种包装应用程序外观的方法。`Qt4.3`提供了8种样式，还有`QStyle`的抽象基类和便利的`QCommonStyle`基类：

- `QStyle`
  - `QCommonStyle`
    - `QWindowsStyle`
      - `QWindowsXPStyle`
        - `QWindowsVistaStyle`
      - `QCleanlooksStyle`
      - `QMacStyle`
      - `QPlastiqueStyle`
    - `QMotifStyle`
      - `QCDEStyle`

​	`QStyle`体系能够通过子类化`QStyle`或者以现存的样式来开发新的自定义外观。在现存的样式上，可以做最少的修改，或者可以从无到有的创建一种完全自定义的样式。

​	`QStyle API`包含绘制图形元素（`drawPrimitive()`、`drawControl()`、`drawComplexControl()`等）的函数，以及样式查询函数（`pixelMetrics()`、`styleHint()`、`hitTest()`等）。`QStyle`成员函数典型的带有`QStyleOption`对象，它包含绘制窗口部件的通用信息（如其调色板）以及特有信息（如按钮的文字）。函数还包含一个可选的`QWidget`指针，以应对`QStyleOption`不能提供全部所需信息的情况。

​	假如我们创建一个不从`QPushButton`继承的自定义按钮类`MyPushButton`。在它的绘图事件处理器中，我们将创建一个`QStyleOption`（实际上是`QStyleOptionButton`），并且会调用`QStyle::drawControl()`。

~~~c++
void MyPushButton::paintEvent(QPaintEvent* event)
{
    QPainter painter(this);
    
    QStyleOptionButton option;
    option.initForm(this);
    if(isFlat())
        option.features |= QStyleOptionButton::Flat;
    option.text = text();
    
    style()->drawControl(QStyle::CE_PushButton, &option, &painter, this);
}
~~~

​	`QStyleOption::initForm()`函数初始化用于显示窗口部件的基本成员变量，例如`rect`、`state`以及调色板。具有特性的`QStyleOptionButton`的成员变量必须被手动的初始化。本例中我们初始化features和text，允许icon和`iconSize`使用默认值。

​	`QWidget::style()`函数返回绘制窗口部件的合适的样式。通常使用`QApplication::setStyle()`设置整个应用程序的样式，但也可以覆盖它，使用`QWidget::setStyle()`为个别窗口部件设置样式。

​	`drawControl()`函数被各种`QStyle`的子类重新实现，用于绘制`QPushButton`和其他的一些简单的窗口部件，典型实现如下图所示：

~~~c++
void QMotifStyle::drawControl(ControlElement element, const QStyleOption* option, QPainter* painter, const QWidget* widget) const
{
    switch(element) {
        case CE_CheckBox:
            ...
        case CE_RadioButton:
            ...
        case CE_PushButton:
            if(const QStyleOptionButton* buttonOption = qstyleoption_cast<const QStyleOptionButton*>(option)) {
                ...
            }
            ...
    }
}
~~~

除了使用`QStyleOption`，`QStyle`的子类也可以直接查询窗口部件：

~~~c++
case CE_PushButton:
	if(const QPushButton* button = qobject_cast<const QPushButton*>(widget)) {
        ...
    }
~~~

### 自定义Style实现

​	当创建一个自定义样式时，通常会基于某种已存在的样式，这样就不用从零开始做所有的事情。例如：

~~~c++
class BronzeStyle : public QProxyStyle
{
	Q_OBJECT

public:
	BronzeStyle() {}

	void polish(QPalette &palette);
	void polish(QWidget *widget);
	void unpolish(QWidget *widget);
	int styleHint(StyleHint which, const QStyleOption *option,
		const QWidget *widget = 0,
		QStyleHintReturn *returnData = 0) const;
	int pixelMetric(PixelMetric which, const QStyleOption *option,
		const QWidget *widget = 0) const;
	void drawPrimitive(PrimitiveElement which,
		const QStyleOption *option, QPainter *painter,
		const QWidget *widget = 0) const;
	void drawComplexControl(ComplexControl which,
		const QStyleOptionComplex *option,
		QPainter *painter,
		const QWidget *widget = 0) const;
	QRect subControlRect(ComplexControl whichControl,
		const QStyleOptionComplex *option,
		SubControl whichSubControl,
		const QWidget *widget = 0) const;

	public slots:
		QIcon standardIconImplementation(StandardPixmap which,
			const QStyleOption *option,
			const QWidget *widget = 0) const;

private:
	void drawBronzeFrame(const QStyleOption *option,
		QPainter *painter) const;
	void drawBronzeBevel(const QStyleOption *option,
		QPainter *painter) const;
	void drawBronzeCheckBoxIndicator(const QStyleOption *option,
		QPainter *painter) const;
	void drawBronzeSpinBoxButton(SubControl which,
		const QStyleOptionComplex *option,
		QPainter *painter) const;
};
~~~

`BronzeStyle`实现了由`QStyle`定义的几个公有函数。`polish()`和`unpolish()`函数会在安装或者卸载样式的时候得到调用。它们可以让我们对窗口部件或调色板做适当的修改。其他的公有函数有的是查询函数(`styleHint()`、`pixelMetric()`、`subControlRect()`)，有的是用于绘图的函数(`drawPrimitive()`、`drawComplexControl()`)。

`BronzeStyle`也提供了一个称为`standardIconImplementation()`的公有槽。这个槽会被Qt的内省机制发现，在必要的时候也可以调用它。Qt有时利用这一惯例来添加虚函数以保证与以前的Qt 4版本的二进制兼容性。在Qt 5中有望用`standardIcon()`虚函数代替它。

该类的详细实现情况请参考`C++ GUI Qt4编程.pdf`。