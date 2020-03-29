# JFreeChart使用

## 1 基本的图表

### 1.1 饼图

​	使用符合`PieDataset`接口的数据创建饼图。

### 1.2 柱状图

​	使用符合`CategoryDataset`接口的数据创建柱状图。

### 1.3 折线图

​	使用符合`CategoryDataset`接口的数据创建折线图。

### 1.4 XY图

​	使用符合`XYDataset`接口的数据创建XY图。标准XY图具有x轴和y轴数字。默认情况下，在每个数据点之间绘制线条。

​	也可以在数据点上绘制点，而不用线连接这些点，这样就是绘制散点图。

​	也可以绘制时间序列图，并可以很简单的像时间序列图中添加移动平均线。

​	使用`HighLowDataset`（`XYDataset`的扩展接口）也可以展示高低开闭的数据。

​	使用`IntervalXYDataset`（`XYDataset`的扩展接口），`JFreeChart`可以在数字域上生成柱状图。

### 1.5 面积图

​	使用符合`CategoryDataset`或`XYDataset`接口的数据创建面积图。

### 1.6 差异图

​	差异图高亮显示两条线路之间的差异。

### 1.7 步骤图

​	步骤图将数字数据显示为“步骤”序列。 根据`XYDataset`中的数据生成步骤图。

### 1.8 甘特图

​	使用符合`IntervalCategoryDataset`接口的数据创建甘特图。

### 1.9 多轴图表

​	可以向图表添加多个域和范围轴。CategoryPlot类和XYPlot类支持此功能。

### 1.10 组合图

​	JFreeChart支持组合图，包括叠加图和组合范围图。

## 2 `JFreeChart`的使用方法

​	使用`JFreeChart`创建一个图表，需要遵循三个步骤：

1. 创建一个数据集`dataset`，这个数据集包含了要在图表中显示的数据；
2. 创建一个`JFreeChart`对象，这个对象将负责绘制图表；
3. 将图表绘制到一个输出对象上（例如，屏幕上的一个Panel）；

一个简单的示例：

~~~java
import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartFrame;
import org.jfree.chart.JFreeChart;
import org.jfree.data.general.DefaultPieDataset;

public class First {
    public static void main(String[] args) {
        // create a dataset
        DefaultPieDataset data = new DefaultPieDataset();
        data.setValue("Category 1", 33.2);
        data.setValue("Category 2", 27.9);
        data.setValue("Category 3", 75.6);

        // create a chart
        JFreeChart chart = ChartFactory.createPieChart("Sample Pie Chart", data, true,
                true, false);

        // create and display a frame
        ChartFrame frame = new ChartFrame("First", chart);
        frame.pack();
        frame.setVisible(true);
    }
}
~~~

输出结果为：

![1582450764254](.\sample.png)

## 3 柱状图

​	大部分柱状图可以使用符合`CategoryDataset`接口的数据来创建，也可以使用符合`IntervalXYDataset`接口的数据来创建。

### 3.1 柱状图示例

​	这个示例中我们使用`String`对象作为数据值的行键和列键。 也可以将实现Comparable接口的任何类用作数据值的键。

~~~java
package BarChart;

import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartPanel;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.axis.NumberAxis;
import org.jfree.chart.plot.CategoryPlot;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.renderer.category.BarRenderer;
import org.jfree.data.category.CategoryDataset;
import org.jfree.data.category.DefaultCategoryDataset;
import org.jfree.ui.ApplicationFrame;
import org.jfree.ui.RefineryUtilities;

import java.awt.*;

public class BarChartDemo extends ApplicationFrame {
    /**
     * Creates a new demo interface
     */
    public BarChartDemo(String title) {
        super(title);

        CategoryDataset dataset = createDataset();
        JFreeChart chart = createChart(dataset);

        // add the chart to a panel
        ChartPanel panel = new ChartPanel(chart);
        panel.setPreferredSize(new Dimension(500, 270));
        setContentPane(panel);
    }

    private CategoryDataset createDataset() {
        // row keys
        String series1 = "First";
        String series2 = "Second";
        String series3 = "Third";

        // column keys
        String category1 = "Category 1";
        String category2 = "Category 2";
        String category3 = "Category 3";
        String category4 = "Category 4";
        String category5 = "Category 5";

        // create the dataset
        DefaultCategoryDataset dataset = new DefaultCategoryDataset();
        dataset.setValue(1.0, series1, category1);
        dataset.setValue(4.0, series1, category2);
        dataset.setValue(3.0, series1, category3);
        dataset.setValue(5.0, series1, category4);
        dataset.setValue(5.0, series1, category5);

        dataset.setValue(5.0, series2, category1);
        dataset.setValue(7.0, series2, category2);
        dataset.setValue(6.0, series2, category3);
        dataset.setValue(8.0, series2, category4);
        dataset.setValue(4.0, series2, category5);

        dataset.setValue(4.0, series3, category1);
        dataset.setValue(3.0, series3, category2);
        dataset.setValue(2.3, series3, category3);
        dataset.setValue(3.4, series3, category4);
        dataset.setValue(6.5, series3, category5);

        return dataset;
    }

    private JFreeChart createChart(CategoryDataset dataset) {
        // create the chart
        JFreeChart chart = ChartFactory.createBarChart(
                "Bar Chart Demo",       // title
                "Category", // categoryAxisLabel
                "Value",       // valueAxisLabel
                dataset,
                PlotOrientation.VERTICAL,
                true,                 // legend
                true,                // tooltips
                false                   // urls
        );

        // now do some optional customisation of the chart...

        // set the background color for the chart
        chart.setBackgroundPaint(new Color(0xBBBBDD));

        // get a reference to the plot for further customisation
        CategoryPlot plot = chart.getCategoryPlot();

        // set the range axis to display integers only
        NumberAxis rangeAxis = (NumberAxis)plot.getRangeAxis();
        rangeAxis.setStandardTickUnits(NumberAxis.createIntegerTickUnits());

        // disable bar outlines
        BarRenderer renderer = (BarRenderer)plot.getRenderer();
        renderer.setDrawBarOutline(false);

        // set up gradient paints for series
        GradientPaint gp0 = new GradientPaint(0.0f, 0.0f, Color.blue, 0.0f, 0.0f, Color.lightGray);
        GradientPaint gp1 = new GradientPaint(0.0f, 0.0f, Color.green, 0.0f, 0.0f, Color.lightGray);
        GradientPaint gp2 = new GradientPaint(0.0f, 0.0f, Color.red, 0.0f, 0.0f, Color.lightGray);
        renderer.setSeriesPaint(0, gp0);
        renderer.setSeriesPaint(1, gp1);
        renderer.setSeriesPaint(2, gp2);

        // optional customisation completed

        return chart;
    }

    public static void main(String[] args) {
        BarChartDemo demo = new BarChartDemo("Bar Chart Demo");
        demo.pack();
        RefineryUtilities.centerFrameOnScreen(demo);
        demo.setVisible(true);
    }
}
~~~

​	输出结果：

![1582454746707](.\BarChart.png)

### 3.2 自定义柱状图

### 3.2.1 柱状图颜色

​	获得对渲染器的引用，并在那里设置柱状图的颜色：

~~~java
CategoryPlot plot = chart.getCategoryPlot();
BarRenderer renderer = (BarRenderer)plot.getRenderer();
renderer.setSeriesPaint(0, Color.red);
renderer.setSeriesPaint(0, Color.blue);
renderer.setSeriesPaint(0, Color.green);
~~~

#### 3.2.2 柱状图间隔

​	可以配置沿类别轴(category)的分布方式：

1. 第一个类别之前的边距；
2. 类别之间的边距；
3. 最后一个类别之后的边距；
4. 同一类别中不同条之间的间隔；

前面三个可以通过`CategoryAxis`进行配置：

~~~java
CategoryPlot plot = chart.getCategoryPlot();
CategoryAxis axis = plot.getDomainAxis();
axis.setLowerMargin(0.02);	// two percent
axis.setCategoryMargin(0.10); // ten percent
axis.setUpperMargin(0.02);	// two percent
~~~

​	所有指定的边距都是类别轴长度的百分比，这是为了让`JFreeChart`可以绘制动态大小的图表。类别之间的边距是所有类别之间边距的总和。如果有N组类别，那么两个类别之间的边距就是N - 1个间隔。

​	第四个边距可以通过`BarRenderer`进行设置：

~~~java
BarRenderer renderer = (BarRenderer)plot.getRenderer();
renderer.setItemMargin(0.15);	// fifteen percent
~~~

​	这里也是设置的图表中所有类别所有条之间的间隔总和。如果有N组类别，每个类别有M条，那么两个条之间的间隔就是（N - 1）（M - 1）个间隔。

​	每个条的宽度是动态计算的，用来填满剩余的空间。无法在`JFreeChart`中指定固定的条形宽度。

## 4 折线图

​	可以使用符合`CategoryDataset`接口或`XYDataset`接口的数据来创建折线图。

### 4.1 使用`CategoryDataset`创建折线图

~~~java
package LineChart;

import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartPanel;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.axis.NumberAxis;
import org.jfree.chart.labels.ItemLabelPosition;
import org.jfree.chart.plot.CategoryPlot;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.renderer.category.LineAndShapeRenderer;
import org.jfree.chart.title.LegendTitle;
import org.jfree.data.category.CategoryDataset;
import org.jfree.data.category.DefaultCategoryDataset;
import org.jfree.ui.ApplicationFrame;
import org.jfree.ui.RefineryUtilities;

import java.awt.*;

public class LineChartDemo1 extends ApplicationFrame {

    public LineChartDemo1(String title) {
        super(title);

        CategoryDataset dataset = createDataset();
        JFreeChart chart = createChart(dataset);

        // add the chart to a panel
        ChartPanel chartPanel = new ChartPanel(chart);
        chartPanel.setPreferredSize(new Dimension(500, 270));
        setContentPane(chartPanel);
    }

    public CategoryDataset createDataset() {
        // row keys
        String series1 = "First";
        String series2 = "Second";
        String series3 = "Third";

        // column keys
        String type1 = "Type 1";
        String type2 = "Type 2";
        String type3 = "Type 3";
        String type4 = "Type 4";
        String type5 = "Type 5";
        String type6 = "Type 6";
        String type7 = "Type 7";
        String type8 = "Type 8";

        // create the dataset
        DefaultCategoryDataset dataset = new DefaultCategoryDataset();
        dataset.addValue(1.0, series1, type1);
        dataset.addValue(4.0, series1, type2);
        dataset.addValue(3.0, series1, type3);
        dataset.addValue(5.0, series1, type4);
        dataset.addValue(5.0, series1, type5);
        dataset.addValue(7.0, series1, type6);
        dataset.addValue(7.0, series1, type7);
        dataset.addValue(8.0, series1, type8);

        dataset.addValue(5.0, series2, type1);
        dataset.addValue(7.0, series2, type2);
        dataset.addValue(6.0, series2, type3);
        dataset.addValue(8.0, series2, type4);
        dataset.addValue(4.0, series2, type5);
        dataset.addValue(4.0, series2, type6);
        dataset.addValue(2.0, series2, type7);
        dataset.addValue(1.0, series2, type8);

        dataset.addValue(4.0, series3, type1);
        dataset.addValue(3.0, series3, type2);
        dataset.addValue(2.0, series3, type3);
        dataset.addValue(3.0, series3, type4);
        dataset.addValue(6.0, series3, type5);
        dataset.addValue(3.0, series3, type6);
        dataset.addValue(2.0, series3, type7);
        dataset.addValue(3.0, series3, type8);

        return dataset;
    }

    private JFreeChart createChart(CategoryDataset dataset) {
        // create the chart
        JFreeChart chart = ChartFactory.createLineChart(
                "Line Chart Demo 1",
                "Type",
                "Value",
                dataset,
                PlotOrientation.VERTICAL,
                true,
                true,
                false
        );

        // now do some optional customisation of the chart
        chart.setBackgroundPaint(new Color(0xCC, 0xCC, 0xFF));

        CategoryPlot plot = chart.getCategoryPlot();

        // customise the range axis
        NumberAxis rangeAxis = (NumberAxis)plot.getRangeAxis();
        rangeAxis.setStandardTickUnits(NumberAxis.createIntegerTickUnits());
        rangeAxis.setAutoRangeIncludesZero(true);
        rangeAxis.setUpperMargin(0.20);
        rangeAxis.setLabelAngle(Math.PI / 2.0);

        // customise the renderer
        LineAndShapeRenderer renderer = (LineAndShapeRenderer)plot.getRenderer();

        renderer.setSeriesStroke(0, new BasicStroke(2.0f, BasicStroke.CAP_ROUND, BasicStroke.JOIN_ROUND,
                1.0f, new float[]{10.0f, 6.0f}, 0.0f));
        renderer.setSeriesStroke(1, new BasicStroke(2.0f, BasicStroke.CAP_ROUND, BasicStroke.JOIN_ROUND,
                1.0f, new float[]{6.0f, 6.0f}, 0.0f));
        renderer.setSeriesStroke(2, new BasicStroke(2.0f, BasicStroke.CAP_ROUND, BasicStroke.JOIN_ROUND,
                1.0f, new float[]{2.0f, 6.0f}, 0.0f));

        renderer.setBaseItemLabelsVisible(true);
        renderer.setBasePositiveItemLabelPosition(new ItemLabelPosition());
        renderer.setBaseNegativeItemLabelPosition(new ItemLabelPosition());
        // optional customisation completed

        return chart;
    }

    public static void main(String[] args) {
        LineChartDemo1 demo = new LineChartDemo1("Line Chart Demo");
        demo.pack();
        RefineryUtilities.centerFrameOnScreen(demo);
        demo.setVisible(true);
    }
}
~~~

运行结果为：

![1582457669511](.\LineChart.png)

### 4.2 使用`XYDataset`创建折线图

​	基于XYDataset的折线图将每个（x，y）点用一条直线相连。

~~~java
package LineChart;

import org.jfree.chart.ChartFactory;
import org.jfree.chart.ChartPanel;
import org.jfree.chart.JFreeChart;
import org.jfree.chart.axis.NumberAxis;
import org.jfree.chart.plot.PlotOrientation;
import org.jfree.chart.plot.XYPlot;
import org.jfree.chart.renderer.xy.StandardXYItemRenderer;
import org.jfree.chart.renderer.xy.XYLineAndShapeRenderer;
import org.jfree.data.xy.XYDataset;
import org.jfree.data.xy.XYSeries;
import org.jfree.data.xy.XYSeriesCollection;
import org.jfree.ui.ApplicationFrame;
import org.jfree.ui.RectangleInsets;
import org.jfree.ui.RefineryUtilities;

import java.awt.*;

public class LineChartDemo2 extends ApplicationFrame {

    public LineChartDemo2(String title) {
        super(title);

        XYDataset dataset = createDataset();
        JFreeChart chart = createChart(dataset);

        ChartPanel chartPanel = new ChartPanel(chart);
        chartPanel.setPreferredSize(new Dimension(500, 270));
        setContentPane(chartPanel);
    }

    private XYDataset createDataset() {
        XYSeries series1 = new XYSeries("First");
        series1.add(1.0, 1.0);
        series1.add(2.0, 4.0);
        series1.add(3.0, 3.0);
        series1.add(4.0, 5.0);
        series1.add(5.0, 5.0);
        series1.add(6.0, 7.0);
        series1.add(7.0, 7.0);
        series1.add(8.0, 8.0);

        XYSeries series2 = new XYSeries("Second");
        series2.add(1.0, 5.0);
        series2.add(2.0, 7.0);
        series2.add(3.0, 6.0);
        series2.add(4.0, 8.0);
        series2.add(5.0, 4.0);
        series2.add(6.0, 4.0);
        series2.add(7.0, 3.0);
        series2.add(8.0, 1.0);

        XYSeries series3 = new XYSeries("Third");
        series3.add(1.0, 4.0);
        series3.add(2.0, 3.0);
        series3.add(3.0, 2.0);
        series3.add(4.0, 3.0);
        series3.add(5.0, 6.0);
        series3.add(6.0, 3.0);
        series3.add(7.0, 4.0);
        series3.add(8.0, 3.0);

        XYSeriesCollection dataset = new XYSeriesCollection();
        dataset.addSeries(series1);
        dataset.addSeries(series2);
        dataset.addSeries(series3);

        return dataset;
    }

    private JFreeChart createChart(XYDataset dataset) {
        // create the chart
        JFreeChart chart = ChartFactory.createXYLineChart(
                "Line Chart Demo 2",
                "X",
                "Y",
                dataset,
                PlotOrientation.VERTICAL,
                true,
                true,
                false
        );

        // now do some optional customisation of the chart
        chart.setBackgroundPaint(Color.white);

        // get a reference to the plot for further information
        XYPlot plot = chart.getXYPlot();
        plot.setBackgroundPaint(Color.lightGray);
//        plot.setAxisOffset(new RectangleInsets(RectangleInsets.));
        plot.setDomainGridlinePaint(Color.white);
        plot.setRangeGridlinePaint(Color.white);

        XYLineAndShapeRenderer renderer = (XYLineAndShapeRenderer)plot.getRenderer();
        renderer.setBaseShapesVisible(true);
        renderer.setBaseShapesFilled(true);

        // change the auto tick unit selection to integers units only
        NumberAxis rangeAxis = (NumberAxis)plot.getRangeAxis();
        rangeAxis.setStandardTickUnits(NumberAxis.createIntegerTickUnits());
        // optional customisation completed

        return chart;
    }

    public static void main(String[] args) {
        LineChartDemo2 demo = new LineChartDemo2("Line Chart Demo");
        demo.pack();
        RefineryUtilities.centerFrameOnScreen(demo);
        demo.setVisible(true);
    }
}
~~~

 运行结果为：

![1582458754404](.\LineChart2.png)