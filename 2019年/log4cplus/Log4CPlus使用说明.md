# `Log4CPlus`使用说明

## 一、程序配置

### 1、主要类说明

| 类名         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| `Filter`     | 过滤器，过滤输出消息。过滤器，解决哪些信息需要输出的问题，比如`DEBUG`，`WARN`, `INFO`等的输出控制 |
| `Layout`     | 布局器，控制输出消息的格式。格式化输出信息,解决了如何输出的问题。 |
| `Appender `  | 挂接器，与布局器和过滤器紧密配合，将特定格式的消息过滤后输出到所挂接的设备终端如屏幕，文件等等)。接收日志的各个设备,如控制台、文件、网络等。解决了输出到哪里去的问题 |
| `Logger `    | 记录器，保存并跟踪对象日志信息变更的实体，当你需要对一个对象进行记录时，就需要生成一个logger。日志模块,程序中唯一一个必须得使用的模块，解决了在哪里使用日志的问题。 |
| `Hierarchy ` | 分类器，层次化的树型结构，用于对被记录信息的分类，层次中每一个节点维护一个logger的所有信息。 |
| `LogLevel `  | 优先权，包括`TRACE`,  `DEBUG`, `INFO`, `WARNING`, `ERROR`, `FATAL`。 |

### 2、使用方法

#### 2.1 基本步骤

使用`log4cplus`有六个基本步骤：

- 步骤一：实例化一个封装了输出介质的`Appender`对象；
- 步骤二：实例化一个封装了输出格式的`Layout`对象；
- 步骤三：将`Layout`对象绑定到`Appender`对象。如果省略该步骤，默认将`SimpleLayout`对象绑定到`Appender`对象；
- 步骤四：实例化一个封装了日志输出的`Logger`对象，并调用其静态函数`getInstance()`获得实例；
- 步骤五：将`Appender`对象绑定到`Logger`对象；
- 步骤六：设置`Logger`的优先级；如果省略该步骤，默认输出所有级别的日志；

标准使用方法：

~~~c++
int main()
{
	// step1 Instantiate an append object
	SharedObjectPtr<Appender> _append(new ConsoleAppender());
	_append->setName("append for test");

	// step2: Instantiate an layout object
	std::string pattern = "%d{%m/%d/%y %H:%M:%S} - %m[%l]%n";

	// step3: Attach the layout object to the append object
	_append->setLayout(std::unique_ptr<Layout>(new PatternLayout(pattern)));

	// step4: Instantiate an logger object
	Logger _logger = Logger::getInstance("test");

	// step5: Attach the appender object to the logger
	_logger.addAppender(_append);

	// step6: Set a priority for the logger
	_logger.setLogLevel(ALL_LOG_LEVEL);

	LOG4CPLUS_DEBUG(_logger, "This is the first log message...");
	std::this_thread::sleep_for(std::chrono::milliseconds(1000));
	LOG4CPLUS_WARN(_logger, "This is the second log message...");

	return 0;
}
~~~

将日志输出到本地文件：

~~~c++
int main()
{
	SharedAppenderPtr _append(new FileAppender("Test.log"));
	_append->setName("filelogtest");

	Logger _logger = Logger::getInstance("test.subtestof_filelog");
	_logger.addAppender(_append);

	for (int i = 0; i < 5; ++i)
	{
		LOG4CPLUS_DEBUG(_logger, "Entering loop#" << i << "Endline#");
	}

	return 0;
}
~~~

### 3、日志输出宏

~~~c++
LOG4CPLUS_TRACE_METHOD(logger,logEvent)

LOG4CPLUS_TRACE(logger,logEvent)
LOG4CPLUS_TRACE_STR(logger,logEvent)
    
LOG4CPLUS_DEBUG(logger,logEvent)
LOG4CPLUS_DEBUG_STR(logger,logEvent)

LOG4CPLUS_INFO(logger,logEvent)
LOG4CPLUS_INFO_STR(logger,logEvent)

LOG4CPLUS_WARN(logger,logEvent)
LOG4CPLUS_WARN_STR(logger,logEvent)

LOG4CPLUS_ERROR(logger,logEvent)
LOG4CPLUS_ERROR_STR(logger,logEvent)

LOG4CPLUS_FATAL(logger,logEvent)
LOG4CPLUS_FATAL_STR(logger,logEvent)
~~~

 其中`logger` 为`Logger`实例名称，`logEvent`为日志内容。由于`log4cplus`选用C++的流机制进行日志输出，因此为了区分包含<<运算符和不包含<<运算符的日志内容，分别提供了`LOG4CPLUS_XXXX`和`LOG4CPLUS_XXXX_STR`两种日志输出宏。 另外，日志输出宏`LOG4CPLUS_TRACE_METHOD`主要用来跟踪方法的调用轨迹。 

### 4、输出格式控制`Layout`

`log4cplus`通过布局器`Layout`来控制输出格式，`log4cplus`提供了三种类型的布局器，分别是`SimpleLayout`、`PatternLayout`和`TTCCLayout`。

#### 4.1 `SimpleLayout`

这是一种简单格式的布局器，在输出的原始信息之前加上`LogLevel`和`-`。如果初始化时没有将布局器挂载到`Appender`对象中，则默认使用`SimpleLayout`。

#### 4.2 `PatternLayout`

一种有词法分析功能的模式布局器，类似于C语言的`printf()`函数，能够对预定义的转换标识符进行解析，转换成特定格式输出。

~~~c++
std::string pattern = "%d{%m/%d/%y %H:%M:%S} - %m[%l]%n";
_append->setLayout(std::unique_ptr<Layout>(new PatternLayout(pattern)));
~~~

`PatternLayout`支持的转换标识符主要包括：

| 标识符   |      | 描述                                                         |
| -------- | ---- | ------------------------------------------------------------ |
| `%%`     |      | 转义为`%`                                                    |
| `%c`     |      | 输出`logger`名称。比如std::string pattern ="%c" 时输出: `"test_logger.subtest"`，也可以控制logger名称的显示层次，比如"%c{1}"时输出"test_logger"，其中数字表示层次 |
| `%D`     |      | 显示本地时间，当std::string pattern ="%D" 时输出:`"2004-10-16 18:55:45"` |
| `%d`     |      | 显示标准时间，所以当std::string pattern ="%d" 时输出`"2004-10-16 10:55:45"` （因为北京时间位于东8区，差8个小时）。可以通过%d{...}定义更详细的显示格式，比如%d{%H:%M:%s}表示要显示小时:分钟：秒。大括号中可显示的预定义标识符如下 |
|          | %a   | 表示礼拜几，英文缩写形式，比如"Fri"                          |
|          | %A   | 表示礼拜几，比如"Friday"                                     |
|          | %b   | 表示几月份，英文缩写形式，比如"Oct"                          |
|          | %B   | 表示几月份，"October"                                        |
|          | %c   | 标准的日期＋时间格式，如 `Sat Oct 16 18:56:19 2004`          |
|          | %d   | 表示今天是这个月的第几天（1 - 31）。                         |
|          | %H   | 表示当前的小时数(0 - 23)                                     |
|          | %I   | 当前当前的小时数（1 - 12）。                                 |
|          | %j   | 表示当前是今年的第几天（1 - 366）。                          |
|          | %m   | 表示月份数（1 - 12）。                                       |
|          | %M   | 表示当前时刻的分钟数（0 - 59）。                             |
|          | %p   | 表示上午或下午。`AM`或`PM`。                                 |
|          | %q   | 表示当前时刻中的毫秒部分（0 - 999）。                        |
|          | %Q   | 表示当前时刻中带小数的毫秒部分（0 - 999.999）。              |
|          | %S   | 表示当前时刻的秒数(0-59)。                                   |
|          | %U   | 表示本周是今年的第几周。以周日为第一天开始计算（0-53）。     |
|          | %w   | 表示周几（0-6），0表示礼拜日。                               |
|          | %W   | 表示本周是今年的第几周。以周一为第一天开始计算（0-53）。     |
|          | %x   | 标准的日期格式。比如`10/26/19`                               |
|          | %X   | 标准的时间格式。比如`09:14:36`                               |
|          | %y   | 两位数的年份。比如19                                         |
|          | %Y   | 四位数的年份。比如2019                                       |
|          | %Z   | 时区名。比如`GMT`。                                          |
| %F       |      | 输出当前记录器所在的文件名称，比如std::string pattern ="%F" 时输出:` "main.cpp"` |
| %L       |      | 输出当前记录器所在的文件行号，比如std::string pattern ="%L" 时输出: "51" |
| %I       |      | 输出当前记录器所在的文件名称和行号，比如std::string pattern ="%l" 时输出`"main.cpp:51"` |
| %m       |      | 输出原始信息，比如std::string pattern ="%m" 时输出:` "teststr"`，即上述代码中`LOG4CPLUS_DEBUG`的第二个参数，这种实现机制可以确保原始信息被嵌入到带格式的信息中 |
| %n       |      | 换行符                                                       |
| %p       |      | 输出`LogLevel`，比如std::string pattern ="%p" 时输出: "DEBUG" |
| %t       |      | 输出记录器所在的线程ID，比如std::string pattern ="%t" 时输出: "1075298944" |
| %x       |      | 嵌套诊断上下文`NDC (nested diagnostic context) `输出，从堆栈中弹出上下文信息，`NDC`可以用对不同源的log信息（同时地）交叉输出进行区分 |
| 格式对齐 |      | 比如std::string pattern =`"%-10m"`时表示左对齐，宽度是10，此时会输出`"teststr  "`，当然其它的控制字符也可以相同的方式来使用，比如`"%-12d"`，`"%-5p"` |

#### 4.3 `TTCCLayout`

是在`PatternLayout`基础上发展的一种缺省的带格式输出的布局器。其格式由时间、线程ID、Logger和`NDC`组成。

`TTCCLayout`在构造时，有机会选择显示本地时间或`GMT`时间，默认按照本地时间显示。

~~~c++
TTCCLayout::TTCCLayout(bool use_gmtime  = false);
~~~

### 5 输出重定向`Appender`

#### 5.1 重定向到控制台

`log4cplus`默认输出到控制台，提供`ConsoleAppender`用于操作。

#### 5.2 重定向到文件

`log4cplus`提供了三个类用于文件操作，它们是`FileAppender`、`RollingFileAppender`、`DailyRollingFileAppender`类。

##### 5.2.1 `FileAppender`

实现了基本的文件操作功能。

~~~c++
FileAppender(const log4cplus::tstring& filename,
                     std::ios_base::openmode mode = std::ios_base::trunc,
                     bool immediateFlush = true,
                     bool createDirs = false);
FileAppender(const log4cplus::helpers::Properties& properties,
                     std::ios_base::openmode mode = std::ios_base::trunc);
~~~

- `filename`：文件名；
- `mode`：文件类型，可选择参数包括`app`、`ate`、`binary`、`in`、`out`、`trunc`；
- `immediateFlush`：缓冲刷新标志。如果为true表示每向文件写一条记录就刷新一次缓存，否则直到`FileAppender`被关闭或文件缓存已满才更新文件。一般要设置为`true`，以防文件没有正常关闭时也可以保证程序终止时刻之前的所有记录都会被正常保存。
- `createDirs`：是否创建目录；

##### 5.2.2 `RollingFileAppender`

实现可以滚动转储的文件操作功能。

~~~c++
RollingFileAppender(const log4cplus::tstring& filename,
                            long maxFileSize = 10*1024*1024, // 10 MB
                            int maxBackupIndex = 1,
                            bool immediateFlush = true,
                            bool createDirs = false);
RollingFileAppender(const log4cplus::helpers::Properties& properties);
~~~

- `maxFileSize`：文件的最大尺寸；
- `maxBackupIndex`：最大记录文件数；

可以根据预先设定的大小来决定是否转储。当超过该大小，后续log信息会另存到新文件中。除了定义每个记录文件的大小外，还要确定在`RollingFileAppender`类对象构造时最多需要多少个这样的记录文件(`maxBackupIndex` + 1)，当存储的文件超过这个数时，会删除最早生成的文件，保证整个文件数目等于(`maxBackupIndex` + 1)。

`log4cplus`中隐含定义了文件的最小尺寸为`200k`，只有大于`200k`的设置才会生效。

##### 5.2.3 `DailyRollingFileAppender`

实现根据频度来决定是否转储的文件操作功能。

~~~c++
DailyRollingFileAppender(const log4cplus::tstring& filename,
                                 DailyRollingFileSchedule schedule = DAILY,
                                 bool immediateFlush = true,
                                 int maxBackupIndex = 10,
                                 bool createDirs = false,
                                 bool rollOnClose = true,
                                 const log4cplus::tstring& datePattern = 			                                      log4cplus::tstring());
DailyRollingFileAppender(const log4cplus::helpers::Properties& properties);
~~~

- `schedule`：存储频度；

`DailyRollingFileAppender`可以根据预先设定的频度来决定是否转储。当超过该频度，后续的`log`信息会另存到新文件中。频度值包括MONTHLY(每月)、WEEKLY(每周)、DAILY(每日)、TWICE_DAILY(每两日)、HOURLY(每小时)、MINUTELY(每分)。

这里的频度并不是指写入文件的速度。是否转储的标准依赖于写入的那一时刻是否满足了频度条件，即是否超过了以分钟、小时、日、周、月为单位的时间刻度，如果超过了就另存。

#### 5.3 重定向到远程服务器

省略。

### 6 嵌入诊断上下文`NDC`

`log4cplus`中的嵌入诊断上下文（Nested Diagnostic  Context），即`NDC`。对log系统而言，当输入源可能不止一个，而只有一个输出时，往往需要分辩所要输出消息的来源，比如服务器处理来自不同客户端的消息时就需要作此判断，`NDC`可以为交错显示的信息打上一个标记(stamp)，使得辨认工作看起来比较容易些。这个标记是线程特有的，利用了线程局部存储机制，称为线程私有数据（Thread-Specific Data，或`TSD`）。

基本使用方法：

~~~c++
int main()
{
	log4cplus::Initializer initializer;

	SharedAppenderPtr _appender(new ConsoleAppender());
	_appender->setLayout(std::unique_ptr<Layout>(new TTCCLayout()));

	log4cplus::Logger logger = log4cplus::Logger::getInstance("NDCLogger");
	logger.addAppender(_appender);

	LOG4CPLUS_DEBUG(logger, "This is the first log");

	NDC& ndc = log4cplus::getNDC();
	ndc.push("ur ndc string");
	LOG4CPLUS_DEBUG(logger, "this is a ndc test");
	ndc.pop();

	LOG4CPLUS_DEBUG(logger, "There should be no ndc");

	return 0;
}
~~~

输出结果为：

~~~
0 [13460] DEBUG NDCLogger <> - This is the first log
2 [13460] DEBUG NDCLogger <ur ndc string> - this is a ndc test
4 [13460] DEBUG NDCLogger <> - There should be no ndc
~~~

另外一种更简单的使用方法是在线程中直接用`NDCContextCreator`：

~~~c++
	NDCContextCreator creator("second urc string");
	LOG4CPLUS_DEBUG(logger, "this is a second ndc test");
~~~

### 7 输出过滤`Filter`

#### 7.1 利用日志级别进行输出过滤

日志级别：

| 级别                    | 说明                                                     |
| ----------------------- | -------------------------------------------------------- |
| NOT_SET_LOG_LEVEL ( -1) | 接受缺省的`LogLevel`，如果有父logger则继承它的`LogLevel` |
| ALL_LOG_LEVEL (0)       | 开放所有log信息输出                                      |
| TRACE_LOG_LEVEL (0)     | 开放trace信息输出(即ALL_LOG_LEVEL)                       |
| DEBUG_LOG_LEVEL(10000)  | 开放debug信息输出                                        |
| INFO_LOG_LEVEL (20000)  | 开放info信息输出                                         |
| WARN_LOG_LEVEL (30000)  | 开放warning信息输出                                      |
| ERROR_LOG_LEVEL(40000)  | 开放error信息输出                                        |
| FATAL_LOG_LEVEL (50000) | 开放fatal信息输出                                        |
| OFF_LOG_LEVEL (60000)   | 关闭所有log信息输出                                      |

在`log4cplus`中，所有logger都通过一个层次化的结构（其实内部是hash表）来组织的，有一个Root级别的logger，可以通过以下方法获取：`Logger root = Logger::getRoot();`

用户定义的logger都有一个名字与之对应，比如：`Logger test = Logger::getInstance("test");`

可以定义该logger的子logger:` Logger subTest = Logger::getInstance("test.subtest");  `

注意Root级别的logger只有通过`getRoot`方法获取，`Logger::getInstance("root")`获得的是它的子对象而已。有了这些具有父子关系的logger之后可分别设置其`LogLevel`,比如：

~~~c++
root.setLogLevel( ... );
Test.setLogLevel( ... );
subTest.setLogLevel( ... );
~~~

各个logger可以通过`setLogLevel`设置自己的优先级，当某个logger的`LogLevel`设置成NOT_SET_LOG_LEVEL时，该logger会继承父logger的优先级，另外，如果定义了重名的多个logger, 对其中任何一个的修改都会同时改变其它logger。

 `log4cplus`支持编译时候和运行时刻利用日志级别进行输出过滤。编译时刻通过如下的预定义变量进行过滤：

~~~
#define LOG4CPLUS_DISABLE_FATAL
#define LOG4CPLUS_DISABLE_WARN
#define LOG4CPLUS_DISABLE_ERROR
#define LOG4CPLUS_DISABLE_INFO
#define LOG4CPLUS_DISABLE_DEBUG
#define LOG4CPLUS_DISABLE_TRACE 
~~~

 运行时刻的过滤则通过使用Logger的`setLogLevel`设置日志级别进行过滤。 

#### 7.2 利用脚本配置进行输出过滤

 由于`log4cplus`脚本配置中可以设置日志的级别、过滤器Filter，因此它也是进行输出过滤的一种很好的选择。 

#### 7.3 `LogLog`的输出过滤

`Loglog`可以使用`setInternalDebugging()`方法用来控制是否屏蔽输出信息中的调试信息，当输入参数为false则屏蔽，缺省设置为false。 另外方法`setQuietMode()`方法用来控制是否屏蔽所有输出信息，当输入参数为true则屏蔽，缺省设置为false。 

## 二、脚本配置

`log4cplus`通过`PropertyConfigurator`类实现了基于脚本配置的功能。通过脚本可以完成对`logger`、`appender`和`layout`的配置。

使用方式：

~~~c++
PropertyConfigurator::doConfigure("config.properties");
~~~

或者：

~~~c++
PropertyConfigurator config("config.properties");
config.configure();
~~~

* 脚本配置的动态加载

  多线程版本的`log4cplus`提供了实用类`ConfigureAndWatchThread`，该类启动线程对配置脚本进行监控，一旦发现配置脚本被更新则立刻重新加载配置。

  类`ConfigureAndWatchThread`的构造函数定义为：

  ~~~c++
  ConfigureAndWatchThread(const log4cplus::tstring& propertyFile,
                                  unsigned int millis = 60 * 1000);
  ~~~

  `propertyFile`为配置脚本的路径名；`millis`为监控时两次更新检查间隔的时间，单位为毫秒。

### 1、基本配置

#### 1.1 根Logger的配置

`log4cplus.rootLogger=[LogLevel],appenderName, appenderName,...`

#### 1.2 非根Logger的配置

`log4cplus.logger.logger_name=[LogLevel][INHERITED],appenderName,appenderName,...`

`INHERITED`表示继承父`Logger`的日志级别。

### 2、 高级配置

#### 2.1 Appender配置

语法：` log4cplus.appender.appenderName=fully.qualified.name.of.appender.class `。

`fully.qualified.name.of.appender.class `的可用值为：

| 值                                    | 描述         |
| ------------------------------------- | ------------ |
| `log4cplus::ConsoleAppender `         | 终端输出     |
| `log4cplus::FileAppender`             | 一般文件输出 |
| `log4cplus::RollingFileAppender`      | 日志大小输出 |
| `log4cplus::DailyRollingFileAppender` | 日期输出     |
| `log4cplus::SocketAppender `          | 网络端口输出 |

文件通用选项：

| 选项                                                         | 作用                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `ImmediateFlush`                                             | 是否立即刷新（默认为true）                                   |
| `log4cplus.appender.ALL_MSGS.ImmediateFlush=true`            |                                                              |
| `File`                                                       | 使用的文件名                                                 |
| `log4cplus.appender.ALL_MSGS.File=all_msgs.log`              |                                                              |
| `Append`                                                     | 是否追加到之前的文件                                         |
| `log4cplus.appender.ALL_MSGS.Append=true`                    |                                                              |
| `ReopenDelay`                                                | 先将日志缓存起来，等指定时间之后再往文件中插入减少文件的保存次数 |
| `log4cplus.appender.ALL_MSGS.ReopenDelay=10`【单位为秒】     |                                                              |
| `UseLockFile`                                                | 是否使用加锁的方式去写文件，默认是false                      |
| `log4cplus.appender.ALL_MSGS.UseLockFile=true`               |                                                              |
| `LockFile`                                                   | 使用的加锁文件名                                             |
| `log4cplus.appender.ALL_MSGS.LockFile=lock_file.lock`[文件名没有具体要求] |                                                              |
| `Locale`                                                     | 使用的字符集                                                 |
| `log4cplus.appender.ALL_MSGS.Locale=chs`【en，其他参数具体见imbue参数】 |                                                              |
| `Threshold`                                                  | 指定日志消息的输出最低层次                                   |
| `log4cplus.appender.ALL_MSGS.Threshold=DEBUG`                |                                                              |

`DailyRollingFileAppender`的相关配置：

| 选项                                                 | 作用                                                         |
| ---------------------------------------------------- | ------------------------------------------------------------ |
| `Schedule `                                          | 文件保存频率 可选值：MONTHLY,  WEEKLY, DAILY, TWICE_DAILY, HOURLY, MINUTELY |
| `log4cplus.appender.ALL_MSGS.Schedule=MINUTELY `     |                                                              |
| `MaxBackupIndex `                                    | 最多文件个数                                                 |
| `log4cplus.appender.ALL_MSGS.MaxBackupIndex=10 `     |                                                              |
| `DatePattern`                                        | 指定文件名的日期格式 1)`'.'yyyy-MM`: 每月 2)`'.'yyyy-ww`: 每周 3)`'.'yyyy-MM-dd`: 每天  4)`'.'yyyy-MM-dd-a`: 每天两次 			5)`'.'yyyy-MM-dd-HH`: 每小时 			6)`'.'yyyy-MM-dd-HH-mm`: 每分钟 |
| `log4cplus.appender.ALL_MSGS.DatePattern='.'yyyy-ww` | 貌似用这种方式不会重命名文件，应该用MONTHLY      : "%Y-%m"<br/>WEEKLY       : "%Y-%W"<br/>DAILY        : "%Y-%m-%d"<br/>TWICE_DAILY  : "%Y-%m-%d-%p"<br/>HOURLY       : "%Y-%m-%d-%H"<br/>MINUTELY     : "%Y-%m-%d-%H-%M" |

`RollingFileAppender`的相关配置：

| 选项                                            | 作用                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| `MaxFileSize`                                   | 最大文件大小，当小于200kb的时候，默认为200kb，单位有（MB、KB） |
| `log4cplus.appender.ALL_MSGS.MaxFileSize=10`    |                                                              |
| `MaxBackupIndex`                                | 最多文件个数                                                 |
| `log4cplus.appender.ALL_MSGS.MaxBackupIndex=10` |                                                              |

#### 2.2 Filter配置

`Appender`可以附加`Filter`组成的链表，如果`Filter`链中存在过滤器`Filter`，`log4cplus`在输出日志之前将调用链表中`Filter`的过滤方法`decide()`，根据该方法的返回值决定是否过滤该输出日志。

语法：

~~~log
log4cplus.appender.appenderName.Filters.FilterNumber=fully.qualified.name.of.Filter.class
log4cplus.appender.appenderName.Filters.FilterNumber.FilterCondition=value.of.FilterCondition
log4cplus.appender.appenderName.Filters.AcceptOnMatch=true|false
~~~

举例：

~~~log
log4cplus.appender.ALL_MSGS.filters.1=log4cplus::spi::LogLevelRangeFilter
log4cplus.appender.ALL_MSGS.filters.1.LogLevelMin=DEBUG
log4cplus.appender.ALL_MSGS.filters.1.LogLevelMax=FATAL
log4cplus.appender.ALL_MSGS.filters.1.AcceptOnMatch=true
log4cplus.appender.ALL_MSGS.filters.2=log4cplus::spi::DenyAllFilter
~~~

目前`log4cplus`提供的过滤器包括`DenyAllFilter`、`LogLevelMatchFilter`、`LogLevelRangeFilter`和`StringMatchFilter`。

- `DenyAllFilter`过滤掉所有信息；

- `LogLevelMatchFilter`根据特定的日志级别进行过滤。过滤条件包括 `LogLevelToMatch`和`AcceptOnMatch(true|false)`，只有当日志的`LogLevel`值与`LogLevelToMatch`相同，且`AcceptOnMatch`为`true`时采取匹配。
- `LogLevelRangeFilter`根据日志级别的范围进行过滤。过滤条件包括 `LogLevelMin`、`LogLevelMax`和`AcceptOnMatch`，只有当日志的`LogLevel`在`LogLevelMin`、`LogLevelMax`之间，同时`AcceptOnMatch`为`true`时才会匹配。 
- `StringMatchFilter`根据日志内容是否包含特定字符串进行过滤。 过滤条件包括`StringToMatch`和`AcceptOnMatch`，只有当日志包含`StringToMatch`字符串 且`AcceptOnMatch`为`true`时会匹配。

过滤条件处理时后写的条件会被先执行。比如

~~~log
log4cplus.appender.append_1.filters.1=log4cplus::spi::LogLevelMatchFilter
log4cplus.appender.append_1.filters.1.LogLevelToMatch=TRACE
log4cplus.appender.append_1.filters.1.AcceptOnMatch=true
#log4cplus.appender.append_1.filters.2=log4cplus::spi::DenyAllFilter
~~~

会首先执行`filter.2`的过滤条件，关闭所有过滤器后，然后执行`filter.1`匹配`TRACE`信息。

#### 2.3 Layout配置

可以选择不设置、`TTCCLayout`或`PatternLayout`。如果不设置，默认配置`SimpleLayout`。

配置`TTCCLayout`: `log4cplus.appender.ALL_MSGS.layout = log4cplus::TTCCLayout`

配置`PatternLayout`:

~~~log
log4cplus.appender.appender_name.layout = log4cplus::PatternLayout
log4cplus.appender.appender_name.ConversionPattern = %d{%m/%d/%y %H:%M:%S,%Q} [%t] %-5p - %m%n
~~~
