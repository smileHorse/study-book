# 1 JTable类

## 1 表格列 Table Columns 

​	对于Swing表，基本单位不是单个单元格而是一列。支持JTable的模型反映了这种世界观：表格中的列不必全部具有相同的数据类型。有一个TableModel处理表中每个单元格的内容。您还将找到一个TableColumnModel，它跟踪表中列的状态（多少列，总宽度，是否可以选择列等）。

​	存储不同类型数据的能力还影响表绘制数据的方式。每一列都有一个数据类型和一个负责绘制它的类。

​	动态数据和数据库查询完全由显示下方的表模型处理。 那么，您可以使用JTable对象做什么呢？ JTable类使您可以控制表的外观和行为。 您可以控制列的间距，它们的可缩放性，它们的颜色等等。 JTable对象是行选择的源。 通过委托，您可以直接使用JTable对象添加和删除行和列。

## 2 属性 Properties

​	JTable的外观几乎完全通过其属性来操纵。 为了使事情更易于管理，我们将属性分成三个较小的表：一个表用于行，列和单元格属性、 一种用于选择属性、一种是视觉特性。 

### 2.1 行，列和单元格的属性。

|          Property          |    Data type     | get  |  is  | set  |       Default value       |
| :------------------------: | :--------------: | :--: | :--: | :--: | :-----------------------: |
| autoCreateColumnsFromModel |     boolean      |  @   |      |  @   |           false           |
|       autoResizeMode       |       int        |  @   |      |  @   |  AUTO_RESIZE_ALL_COLUMNS  |
|        columnCount         |       int        |  @   |      |      |             0             |
|        columnModel         | TableColumnModel |  @   |      |  @   | DefaultTableColumnModel() |
|           model            |    TableModel    |  @   |      |  @   |    DefaultTableModel()    |
|          rowCount          |       int        |  @   |      |      |             0             |
|         rowHeight          |       int        |  @   |      |  @   |            16             |

​	autoCreateColumnsFromModel属性确定是否使用表模型中的数据自动加载列模型。如果您向构造函数提供为null的ColumnModel，则此值设置为true。 （您始终可以使用setColumnModel（）方法替换现有的列模型。）

​	autoResizeMode属性确定表对调整大小的反应。使用下表中显示的常量，可以调整所有列，仅调整最后一列，或完全关闭调整大小。如果要在滚动窗格中使用水平滚动条，则必须关闭自动调整大小。

|            Constant            | Data type |                         Description                          |
| :----------------------------: | :-------: | :----------------------------------------------------------: |
|    AUTO_RESIZE_ALL_COLUMNS     |    int    |            调整表格大小时，所有列均按比例调整大小            |
|    AUTO_RESIZE_LAST_COLUMN     |    int    |              调整表大小时，仅调整最后一列的大小              |
|    AUTO_RESIZE_NEXT_COLUMN     |    int    | 调整列的大小时，也要调整下一列的大小。例如，如果您将N列增大，则N + 1列会缩小 |
|        AUTO_RESIZE_OFF         |    int    |               调整表大小时，根本不要调整列大小               |
| AUTO_RESIZE_SUBSEQUENT_COLUMNS |    int    | 调整列大小时，请按比例调整所有后续列的大小，以保留表格的整体宽度 |

​	columnCount和rowCount属性使您可以询问JTable对象有多少行和列。这些值来自适当的模型。

​	columnModel属性保存当前的列模型，并且在必要时可以在运行时进行替换。

​	rowHeight属性指示行的高度（以像素为单位）。此属性必须是一个大于或等于1的数字。其他值导致setRowHeight（）方法引发IllegalArgumentException。 rowHeight的值包括垂直小区间间距。

### 2.2 选择相关的属性

|        Property        |     Data type      | get  |  is  | set  |        Default value        |
| :--------------------: | :----------------: | :--: | :--: | :--: | :-------------------------: |
|  cellSelectionEnabled  |      boolean       |  @   |      |  @   |            false            |
| columnSelectionAllowed |      boolean       |  @   |      |  @   |            false            |
|  rowSelectionAllowed   |      boolean       |  @   |      |  @   |            true             |
|     selectedColumn     |        int         |  @   |      |      |             -1              |
|  selectedColumnCount   |        int         |  @   |      |      |              0              |
|    selectedColumns     |       int[]        |  @   |      |      |           int[0]            |
|      selectedRow       |        int         |  @   |      |      |             -1              |
|    selectedRowCount    |        int         |  @   |      |      |              0              |
|      selectedRows      |       int[]        |  @   |      |      |           int[0]            |
|     selectionMode      |        int         |      |      |  @   | MULTIPLE_INTERVAL_SELECTION |
|     selectionModel     | ListSelectionModel |  @   |      |  @   |  DefaultListSelectionModel  |

​	selectionModel属性包含处理行选择的ListSelectionModel对象，而selectionMode属性适用于该模型。 （可以使用表的TableColumnModel的selectionModel属性来控制列选择。）

​	cellSelectionEnabled，columnSelectionAllowed和rowSelectionAllowed属性确定是否可以选择单元格，列或行。如果可以选择单元格，则无论行和列属性如何，都只能选择单元格。在打开单元格选择并且关闭行和列选择的情况下，您仍然可以选择一系列单元格。

​	在表上有一个活动选择时，selectedColumn，selectedColumnCount，selectedColumns，selectedRow，selectedRowCount和selectedRows允许您访问该选择的各个部分。 selectedRow和selectedColumn属性存储来自其各自选择模型的锚点选择（即第一个选择）。

### 2.3 其余的属性

|            Property             |     Data type     | get  |  is  | set  |       Default value        |
| :-----------------------------: | :---------------: | :--: | :--: | :--: | :------------------------: |
|        accessibleContext        | AccessibleContext |  @   |      |      |  JTable.AccessibleJTable   |
|           cellEditor            |  TableCellEditor  |  @   |      |  @   |            null            |
|           dragEnabled           |      boolean      |  @   |      |  @   |           false            |
|            gridColor            |       Color       |  @   |      |  @   |          From L&F          |
|        intercellSpacing         |     Dimension     |  @   |      |  @   |       Dimension(1,1)       |
| preferredScrollableViewportSize |     Dimension     |  @   |      |  @   |    Dimension(450, 400)     |
|            rowMargin            |        int        |  @   |      |  @   |             1              |
| scrollableTracksViewportHeight  |      boolean      |  @   |      |      |           false            |
|  scrollableTracksViewportWidth  |      boolean      |  @   |      |      |           false            |
|       selectionBackground       |       Color       |  @   |      |  @   |          From L&F          |
|       selectionForeground       |       Color       |  @   |      |  @   |          From L&F          |
|            showGrid             |      boolean      |      |      |  @   |            true            |
|       showHorizontalLines       |      boolean      |  @   |      |  @   |            true            |
|        showVerticalLines        |      boolean      |  @   |      |  @   |            true            |
|           tableHeader           |   JTableHeader    |  @   |      |  @   | JTableHeader(column-Model) |
|               UI                |      TableUI      |  @   |      |  @   |          From L&F          |
|            UIClassID            |      String       |  @   |      |      |         "TableUI"          |

​	cellEditor属性确定当前使用的单元格编辑器。当您开始编辑时，JTable类将查找您的列，并要求其提供编辑器。如果该列有一个，则使用该编辑器；否则，将使用该列的类类型的默认编辑器。如果当前未在编辑任何单元格，则此属性为null。

​	如果希望表支持自动拖动启动，请将dragEnabled属性设置为true。 

​	gridColor，selectionBackground和selectionForeground属性确定用于网格线和选择文本的颜色。 intercellSpacing属性确定表中每个单元格周围的水平和垂直间距。

​	preferredScrollableViewportSize属性确定表滚动窗格的首选大小。 scrollableTracksViewportHeight和scrollableTracksViewportWidth属性始终为false，这表明围绕表的视口不应调整表的大小以适合视口（假设您已将表放置在滚动窗格中）。

​	您可以使用showGrid，showHorizontalLines和showVerticalLines控制在表上显示哪些行。使用setShowGrid（）作为同时打开或关闭水平线和垂直线的便捷方法。 

​	 tableHeader属性用于存储表的JTableHeader对象。此标头可以在JScrollPane中用作表的列标头。

​	rowMargin属性确定行之间的空白空间量。实际上，这只是获取intercellSpacing属性中高度信息的一种更便捷的方法。

## 3 事件 Event

​	您希望从JTable类看到的所有特定于表的事件都通过其数据和列模型进行路由。 您必须获得对这些模型的引用，并将侦听器直接附加到这些模型。

~~~java
TableModel myModel = new MyTableModel( );  // Some valid TableModel class
JTable table = new JTable(myModel);
EEL eel = EEL.getInstance( );
eel.addGui( );
// Listen for added/removed/updated rows.
myModel.addTableModelListener(eel);
TableColumnModel columnModel = table.getColumnModel( );
// Listen for added/removed/moved columns.
columnModel.addTableColumnModelListener(eel);
// Listen for row selections.
myTable.getSelectionModel( ).addListSelectionListener(eel);
// Listen for column selections.
columnModel.getSelectionModel.addListSelectionListener(eel);
~~~

## 4 常量 Constants

​	在上面设置autoResizeMode属性时指定的常量，用于在调整整个表的大小或调整单个列时的列行为。 

## 5 构造函数

~~~java
public JTable( ) 
// 使用DefaultTableModel，DefaultTableColumnModel和DefaultListSelectionModel作为其模型，创建一个新的JTable对象。

public JTable(TableModel dm) 
public JTable(TableModel dm, TableColumnModel cm) 
public JTable(TableModel dm, TableColumnModel cm, ListSelectionModel sm) 
// 这些构造函数允许您指定要使用的确切表模型，表列模型和（行）列表选择模型。 如果只想指定列或列表选择模型，则可以将null作为其他模型的参数传递，并创建并使用适当的默认模型。

public JTable(int numRows, int numColumns) 
// 此构造函数使用指定的行数和列数来构建默认表模型。 还使用默认的表列和列表选择模型。 

public JTable(Vector data, Vector columnNames) 
public JTable(Object data[][], Object columnNames[]) 
// 通过使用数据填充自定义表模型并使用columnNames命名列来填充表。 在第一个构造函数的情况下，假定data是一个包含其他vector的vector，每行数据一个。 data参数可以包含任何类型的对象。 该表会尽最大努力在数组中适当地呈现对象，必要时使用通过在对象上调用toString（）生成的标签。 虽然columnNames参数还可以包含任何类型的对象的数组，但String []是最常见的。 默认的表头渲染器使用列名（或非字符串对象的toString（）结果）来标记列。
// 这些构造函数使用的表模型不是DefaultTable-Model的实例。 如果检索表模型，则只能通过TableModel接口与之交互。
~~~

## 6 其他有意思的函数

​	这些方法允许您以编程方式控制表。 如果您还有其他按钮或键盘快捷键来执行类似选择匹配记录的操作，这些方法将派上用场。

~~~java
public void addColumnSelectionInterval(int index0, int index1)
public void addRowSelectionInterval(int index0, int index1) 
// 以编程方式扩展表上的列和行选择。 必须将不连续的选择添加为单独的连续间隔。 index0的值不必小于或等于index1，但是如果其中一个索引超出有效列的范围，则将导致IllegalArgumentException。

public void clearSelection( ) 
// 此方法删除表中当前活动的所有选择间隔（行和列）。

public int columnAtPoint(Point p) 
// 给定相对于此JTable组件的左上角的（x，y）坐标p，返回包含该点的列。 （如果表的任何列中都不包含p，则此方法返回-1。）与rowAtPoint（）方法一起，您可以利用此方法来跟踪鼠标在表中的位置。

public boolean editCellAt(int row, int col, EventObject event) 
// 打开（行，列）的单元格进行编辑。 花哨的编辑人员可以根据作为事件传递的启动操作来更改其行为或外观。 如果不需要特定的事件，则可以创建一个MOUSE_CLICKED类型的简单MouseEvent，其点击计数为2。

public void moveColumn(int column, int targetColumn) 
// 此方法将column处的列重新定位为targetColumn处的列。 如果您具有允许用户将列与表本身分开的布局向导，这将很方便。

public void removeColumn(TableColumn column) 
// 此方法使您可以完全删除列。

public void removeColumnSelectionInterval(int index0, int index1)
public void removeRowSelectionInterval(int index0, int index1) 
// 从活动选择中减去选择间隔（分别为列或行）。 此操作与清除选择不同。 如果当前选择的项目比删除的项目多，其余项目保持选中状态。

public int rowAtPoint(Point p) 
// 给定相对于此JTable组件的左上角的（x，y）坐标p，返回包含该点的行。 （如果表的任何行中都不包含p，则此方法返回-1。）与columnAtPoint（）方法一起，您可以利用此方法来跟踪鼠标在表中的位置。

public void selectAll( ) 
// 选择表中的所有单元格（或所有行和所有列，如果需要）。 该方法与clearSelection（）方法相反。 是否将此选择报告为行，列或单元格取决于如何设置columnSelectionAllowed和rowSelectionAllowed属性。
~~~

## 7 TableColumn类

​	TableColumn类是构建列的起点。 它提供对单个列的所有基本组件的访问。 此类不应与TableColumnModel接口混淆。 下一节将讨论的模型规定了表列集合的形式，然后构成了一个完整的表。

### 7.1 属性

|        Property         |        Data type         | get  |  is  | set  |   Default value   |
| :---------------------: | :----------------------: | :--: | :--: | :--: | :---------------: |
|       cellEditor        |     TableCellEditor      |  @   |      |  @   |       null        |
|      cellRenderer       |    TableCellRenderer     |  @   |      |  @   |       null        |
|     headerRenderer      |    TableCellRenderer     |  @   |      |  @   |       null        |
|       headerValue       |          Object          |  @   |      |  @   |       null        |
|       identifier        |          Object          |  @   |      |  @   |       null        |
|        maxWidth         |           int            |  @   |      |  @   | Integer.MAX_VALUE |
|        minWidth         |           int            |  @   |      |  @   |        15         |
|       modelIndex        |           int            |  @   |      |  @   |         0         |
| propertyChangeListeners | PropertyChangeListener[] |  @   |      |      |    Empty Array    |
|       resizeable        |         boolean          |  @   |      |  @   |       true        |
|          width          |           int            |  @   |      |  @   |        75         |

​	cellEditor，cellRenderer和headerRenderer属性确定哪些组件用于绘制（并可能编辑）单元格值。这些属性的默认值null指示应构建和使用默认渲染器或编辑器。

​	headerValue属性可供headerRenderer访问以绘制适当的标题。

​	identifier属性用于唯一地标识列。如果未指定标识符，则getIdentifier（）方法返回当前的headerValue。

​	minWidth和maxWidth属性确定列的最小和最大宽度（以像素为单位）。通过将这些属性设置为相同的值，可以创建一个固定宽度的列。列的当前宽度存储在width属性中。 

​	modelIndex确定在呈现或编辑列以获取适当的数据值时使用的索引值。通常在构造函数中设置它，此后不需要修改。在屏幕上重新定位列对模型索引没有影响。

​	resizeable属性仅影响用户手动调整列大小的能力，您可以随时以编程方式调整列的大小。

### 7.2 常量

​	其中四个属性具有与属性更改事件一起使用的描述性常数。

|         Constant         |  Type  |        Description         |
| :----------------------: | :----: | :------------------------: |
|  CELL_RENDERER_PROPERTY  | String |  cellRenderer属性的属性名  |
|  COLUMN_WIDHT_PROPERTY   | String |  columnWidth属性的属性名   |
| HEADER_RENDERER_PROPERTY | String | headerRenderer属性的属性名 |
|  HEADER_VALUE_PROPERTY   | String |  headerValue属性的属性名   |

### 7.3 事件

​	TableColumn类生成的唯一事件是属性更改事件，该属性更改事件是在更改任何列的绑定属性（cellRenderer，headerRenderer，headerValue和width）时生成的。

~~~java
public void addPropertyChangeListener(PropertyChangeListener l) 
public void removePropertyChangeListener(PropertyChangeListener l) 
// 添加或删除对从此列接收事件感兴趣的属性更改侦听器。
~~~

### 7.4 构造函数

~~~java
public TableColumn( ) 
// 使用默认属性值创建一个空列。

public TableColumn(int modelIndex) 
// 使用指定的modelIndex创建一个空列。

public TableColumn(int modelIndex, int width) 
// 创建一个具有指定modelIndex和width（以像素为单位）的空列。 minWidth和maxWidth属性保留其默认值。

public TableColumn(int modelIndex, int width, TableCellRenderer cellRenderer, TableCellEditor cellEditor) 
// 使用指定的modelIndex，width（以像素为单位），cellRenderer和cellEditor创建一个空列。 渲染器或编辑器参数可以为null，在这种情况下，将使用适当的默认值。
~~~

### 7.5 其他函数

​	属性的get / set方法构成了TableColumn类的大部分。

~~~java
public void sizeWidthToFit( ) 
// 强制列的宽度匹配其标题的宽度，即使这意味着修改minWidth或maxWidth属性。
~~~

## 8 TableColumnModel 接口

​	为了处理实际的表（甚至只有一个列的表），我们需要一个模型来存储几列作为一个集合。 TableColumnModel接口在Swing包中提供了该功能。 JTable类除了具有表模型外，还具有列模型。 虽然表模型提供了列中单元格的特定值，但列模型提供了诸如列边距以及是否允许选择列之类的信息。

​	TableColumnModel接口管理列选择和列间距。 为了管理选择，您可以访问常规的选择属性，例如，选择的列数和适当的选择模型。 要处理列间距，可以控制列边距并查看总列宽。

### 8.1 属性

|        Property        |     Data type      | get  |  is  | set  | Default value |
| :--------------------: | :----------------: | :--: | :--: | :--: | :-----------: |
|         column         |    TableColumn     |  @   |      |      |               |
|        columns         |    Enumeration     |  @   |      |      |               |
|      columnCount       |        int         |  @   |      |      |               |
|      columnMargin      |        int         |  @   |      |  @   |               |
| columnSelectionAllowed |      boolean       |  @   |      |  @   |               |
|  selectedColumnCount   |        int         |  @   |      |      |               |
|    selectedColumns     |       int[]        |  @   |      |      |               |
|     selectionModel     | ListSelectionModel |  @   |      |      |               |
|    totalColumnWidth    |        int         |  @   |      |  @   |               |

​	columnCount属性返回此模型支持的列数。 尽管这看起来像是多余的信息，但鉴于表模型（本章稍后讨论）知道它支持多少列，下一章将研究一些未使用表模型中所有可用列的列模型。 

​	columnMargin属性指示列之间应保留多少空间。 在计算totalColumnWidth的值时，将包括该间距。

​	您可以使用columnSelectionAllowed属性打开或关闭列选择。 如果允许选择列，则可以使用selectionModel，selectedColumns和selectedColumnCount属性来处理选择。 与其他选择一样，如果需要，您可以使用selectionModel以编程方式影响所选列。

​	column和columns属性使您可以自己访问表的列。 用作getColumn（）参数的索引是指列模型中列的索引，该索引不一定与表模型中列的索引或屏幕上显示列的顺序匹配。

### 8.2 事件

​	任何实现TableColumnModel接口的类都必须支持ColumnModelEvent，它是在列的视图大小，位置或范围大小更改时生成的。 该接口定义了标准的addColumnModelListener（）和removeColumnModelListener（）方法，但是实现类负责在添加，删除或移动列时触发事件的代码。

~~~java
public void addColumnModelListener(TableColumnModelListener l) 
public void removeColumnModelListener(TableColumnModelListener l) 
// 添加或删除对此列模型的更改感兴趣的侦听器。
~~~

### 8.3 列函数

~~~java
public void addColumn(TableColumn column) 
// 将列追加到当前列模型。

public int getColumnIndex(Object identifier) 
public int getColumnIndexAtX(int xPixel) 
// 返回具有标题匹配标识符或在屏幕上指定的xPixel位置的列的列模型（屏幕）索引。

public void moveColumn(int index, int newIndex) 
// 将index处的列移动到newIndex。 其他列应根据需要移动以适应移动的列。 这仅在视觉上重新定位屏幕上的列。 表模型不变。

public void removeColumn(TableColumn column) 
// 从列模型中删除列。 删除的列之后的列将移动一个索引以填补空白。
~~~

## 9 DefaultTableColumnModel 类

​	如果未在JTable构造函数中指定模型，则DefaultTableColumnModel类将实现TableColumnModel接口并用作列模型。 它还可以作为创建自己的列模型的良好起点。 您继承了所需的一切； 只是覆盖您要更改的方法。

### 9.1 属性

DefaultTableColumnModel类从TableColumnModel接口继承其所有属性。

|        Property        |     Data type      | get  |  is  | set  |        Default value        |
| :--------------------: | :----------------: | :--: | :--: | :--: | :-------------------------: |
|         column         |    TableColumn     |  @   |      |      |                             |
|        columns         |    Enumeration     |  @   |      |      |                             |
|      columnCount       |        int         |  @   |      |      |              0              |
|      columnMargin      |        int         |  @   |      |  @   |              1              |
| columnSelectionAllowed |      boolean       |  @   |      |  @   |            false            |
|  selectedColumnCount   |        int         |  @   |      |      |              0              |
|    selectedColumns     |       int[]        |  @   |      |      |            null             |
|     selectionModel     | ListSelectionModel |  @   |      |      | DefaultListSelectionModel() |
|    totalColumnWidth    |        int         |  @   |      |  @   |              0              |

### 9.2 事件

​	DefaultTableColumnModel支持由TableColumnModel接口指示的ColumnModelEvent事件，但它除了简单地附加侦听器外，还包含几种便利的方法：

~~~java
protected void fireColumnAdded(TableColumnModelEvent e) 
protected void fireColumnMarginChanged( ) 
protected void fireColumnMoved(TableColumnModelEvent e) 
protected void fireColumnRemoved(TableColumnModelEvent e) 
protected void fireColumnSelectionChanged(ListSelectionEvent e) 
// 这些辅助方法用于在添加，调整大小，重定位，移除或选择列时触发事件。 它们还允许您通过扩展此类来触发基于列的事件以创建自己的列模型。

public void addColumnModelListener(TableColumnModelListener x) 
public void removeColumnModelListener(TableColumnModelListener x) 
// 添加或删除此模型触发的TableColumnModelEvents的侦听器。

protected void propertyChange(PropertyChangeEvent e) 
protected void valueChanged(ListSelectionEvent e) 
// DefaultTableColumnModel侦听这些事件中的一些事件，以保持表的视觉状态同步。 COLUMN_WIDTH_PROPERTY更改事件（来自添加的TableColumn对象之一）导致重新计算表的宽度缓存。 valueChanged（）方法监听新的列选择并触发列选择改变的事件。
~~~

### 9.3 构造函数

~~~java
public DefaultTableColumnModel( ) 
// 使用属性表列出的属性的默认值设置一个新的DefaultTableColumnModel。
~~~

### 9.4 其他函数

~~~java
public void addColumn(TableColumn column) 
public int getColumnIndex(Object identifier) 
public int getColumnIndexAtX(int xPixel) 
public void moveColumn(int index, int newindex) 
public void removeColumn(TableColumn column) 
// 这些方法提供TableColumnModel接口所需的抽象方法的直接实现。
~~~

## 10 TableColumnModelEvent 类

​	DefaultTableColumnModel类触发的许多事件都使用此事件类对受影响的列进行编码。 注意，与选择事件不同，这些事件描述了一组连续的列中发生的事情。 没有直接的支持，例如删除不连续的列选择。 您必须为每个需要删除的连续范围的列生成一个不同的事件。

### 10.1 事件函数

~~~java
public int getFromIndex( ) 
public int getToIndex( ) 
// 使用这些方法来查找受影响的列。 如果只影响单个列，则这些方法将返回相同的值。
~~~

## 11 TableColumnModelListener 接口

​	如果要侦听任何列模型事件，则必须实现TableColumnModelListener接口并注册为这些事件的侦听器。 毫不奇怪，DefaultTableColumnModel类的事件触发方法反映了此接口定义的事件类型：

~~~java
public void columnAdded(TableColumnModelEvent e) 
public void columnMarginChanged(ChangeEvent e) 
public void columnMoved(TableColumnModelEvent e) 
public void columnRemoved(TableColumnModelEvent e) 
public void columnSelectionChanged(ListSelectionEvent e) 
// 使用这些方法对列模型中的更改做出反应。 如果只关心列选择，则不能将ListSelectionListener直接添加到列模型中，但是可以检索选择模型（使用getSelectionModel（））并将侦听器附加到该对象。
~~~