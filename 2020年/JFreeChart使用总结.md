# JFreeChart使用总结

## 0 工具函数

`ChartUtilities.applyCurrentTheme`

`RefineryUtilities.centerFrameOnScreen`

##  `ChartPanel`

`setMouseWheelEnabled`

`setDomainZoomable`

`setRangeZoomable`

##  `JFreeChart`

`addSubtile(TextTile)`

`setBackgroundImage`

`addProgressListener`

## `CategoryPlot`

`setRangePannable`

`setRangeGridlinesVisibile`

`setBackgroundPaint`

`setOrientation`

`setDrawingSupplier`

##  `XYPlot`

`setDomainPannable(boolean)`

`setRangePannable(boolean)`

`addAnnoation`

`setDomainCrosshairVisible`

`setDomainCrosshairLockedOnData`

`setRangeCrosshairVisible`

`setBackgroundPaint`

`setDomainGridlinePaint`

`setRangeGridlinePaint`

`setAxisOffset`

##  `NumberAxis`

`setUpperMargin`：设置最大值边界扩大系数

`setStandardTickUnits`

`setAutoRangeIncludeZero`

##  `XYTextAnnoation`

`setFont`

`setTextAnchor`

## `TextTitle`

`setFont`

`setPosition`

`setHorizontalAlignment`

## `LineAndShapeRenderer`

`setBaseShapesVisible`：线路连接处是否显示

`setDrawOutlines`

`setUseFillPaint`

`setBaseFillPaint`：线路连接处填充背景色

`setSeriesStroke`

`setSeriesOutlineStroke`

`setSeriesShape`

`setBaseItemLabelsVisible`

`setBaseItemLabelGenerator`

## `XYLineAndShapeRenderer`

`setBaseShapesVisible`：线路连接处是否显示

`setBaseShapesFilled`：线路连接处是否填充

`setSeriesLinesVisible`

`setBaseToolTipGenerator`