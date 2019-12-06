# Qt自定义外观时碰到的问题

# 1 对继承自QWidget的类使用setStyleSheet设置背景图片不起作用的解决方案？

在派生类中重新实现`paintEvent`函数：

~~~c++
void CMyWidget::paintEvent(QPaintEvent * event)
{
	QStyleOption opt;
	opt.init(this);
	QPainter p(this);
	style()->drawPrimitive(QStyle::PE_Widget, &opt, &p, this);
}
~~~

