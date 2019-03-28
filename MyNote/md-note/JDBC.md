## JDBC介绍 ##
JDBC（Java DataBase Connectivity）就是Java数据库连接，是一种可以执行SQL语句的Java API。
通过JDBC API和不同的数据库产商提供的JDBC驱动，我们可以实现用一套Java程序就能访问不同的数据库（数据库操作都是标准SQL语句）。
JDBC驱动就是将JDBC API对数据库的操作映射成特定数据库的调用。

数据库厂商提供的JDBC驱动通常是一个jar包文件。


## JDBC编程步骤 ##

step1. 加载数据库驱动
	
	Class.forName("com.mysql.jdbc.Driver");

step2. 通过DriverManger获取数据库链接

	Connection conn = DriverManager.getConnection("jdbc:mysql://hostname:port/databasename", "username", "password");

step3. 通过步骤2得到的Connection对象创建Statement对象。

	createStatement() 获取一个基本的Statement对象
	prepareStatement(String preSql) 获取一个预编译的Statement对象。

step4. 使用Statement对象执行SQL语句。

	execute(String sql) 执行任意SQL语句
	executeUpdate(String sql) 执行DML和DDL语句。执行DML语句时返回受SQL语句影响的行数。
	executeQuery(String sql) 执行查询语句，返回ResultSet结果集对象。

step5. 操作结果集ResultSet。

	通过ResultSet的next(), previous(), first(), last(), beforeFirst(), afterLast(), absolute()等方法可移动记录指针。
	通过getXxx方法可获取当前记录指针所指行中的某个字段值。

step6. 回收数据库资源。包括关闭ResultSet, Statement和Connection资源。
 
## 事务处理 ##
### 事务的概念 ###
事务是由若干个数据库操作序列组成的一个逻辑执行单元。这一系列的操作要么全部执行，要么全部放弃执行。
程序和事务是两个不同的概念，一般而言，一个程序中可能包含多个事务。

### 事务的4种特性 （ACID） ###
原子性（Atomicity）： 事务是应用中最小的执行单元，就如原子是自然界的最小颗粒具有不可再分的特征一样，事务是应用中不可再分的最小逻辑执行体。

一致性（Consistency）： 事务执行的结果，必须使数据库从一个一致性状态，变到另一个一致性状态。
当数据库只包含事务成功提交的结果时，数据库处于一致性状态。
如果某个事务尚未完成而被迫中断，可是此未完成的事务对数据库所做的修改已被写入数据库，那么此时数据库就处于一种不正确的状态。
所以，一致性是通过原子性来保证的。

隔离性（Isolation）： 各个事务互不干扰。任意一个事务的内部操作对其他并发的事务都是隔离的。

持续性（Durability）： 也称为持久性。事务一旦提交，对数据所做的任何改变都要记录到永久存储器中，通常就是保存进物理数据库。

### 数据库的事务组成语句 ###
一个事务由以下语句组成：
	
	一组DML语句
	一条DDL语句（或一条DCL语句）

如上，DDL语句或DCL语句最多只能有一条，因为DDL和DCL语句都会导致事务立即提交。

事务提交的方式：

	显示提交： 使用commit
	自动提交： 执行DDL或DCL语句，或程序正常退出。
当事务所包含的全部数据库操作都成功执行后，应提交事务，使这些修改永久生效。

事务回滚的方式：

	显示回滚： 使用rollback
	自动回滚： 系统出错或强行退出。
当事务中的某个数据库操作执行失败后，应该回滚事务。

普通的提交或回滚事务，都会结束当前事务。

### MySQL中的事务处理 ###
#### 开启、关闭事务 ####
MySQL默认关闭事务，此时在MySQL控制台输入的每条DML语句都会立即自动提交，保存到数据库中。
在当前命令行窗口开启MySQL的事务支持的方式如下：

	set autocommit=0;
如上，0表示开启事务，关闭自动提交；1则表示关闭事务，开启自动提交。

一个MySQL命令行窗口代表一次连接Session，所以在当前窗口输入set autocommit=0； 只是关闭此窗口对应的连接Session的自动提交，对其他窗口没有影响。

#### 临时性开启事务 ####
如果不想关闭整个命令行窗口的自动提交，可以采用临时性开启事务的方式（采用MySQL提供的start transaction或begin命令）

	begin;
	inset into student_table values(null, 'xx'), 1;
	inset into student_table values(null, 'xx'), 1;
	inset into student_table values(null, 'xx'), 1;
	select * from student_table;
	rollback;
	select * from student_table;

如上，在事务没有提交情况下，begin后面的DML语句不会立即生效，即不会对数据库有任何影响。在执行rollback之前，当前事务还没有结束，所以通过第一条select语句，我们能看到当前事务中插入的3条记录。当执行了rollback后，事务结束，但没有提交，所以最后一条select语句看不到此处插入的3条记录了。

#### 设置事务中间点 ####
MySQL提供savepoint命令设置事务中间点。
使用savepoint设置事务中间点，可以让事务只回滚到指定中间点，而不是回滚全部事务：

	savepoint a;

	rollback to a;
	
回滚到指定中间点还处于事务之中，所以回滚到中间点不会结束当前事务。

### JDBC的事务支持 ###
JDBC通过Connection的API提供事务支持。Connection默认打开自动提交，即关闭事务。
Connection的事务相关API如下：

	void setAutoCommit(boolean autoCommit)  设置是否开启事务
	boolean getAutoCommit()  判断事务是否开启
	commit() 提交事务
	rollback() 回滚事务
	Savepoint setSavepoint() 设置事务中间点
	rollback(Savepoint savepoint) 回滚到指定中间点

## JDBC数据库连接池 ##
数据库连接的建立和关闭是极耗费系统资源的操作。通过DriverManager获取的数据库连接对象对应一个物理数据库连接，频繁地打开和关闭连接将造成系统性能下降。

解决方案： 当启动应用时，系统主动建立足够的数据库连接，并将这些连接组成一个连接池。每次请求数据库连接时，无须重新打开连接，而是从连接池中取出已有的连接使用。使用完后不再关闭数据库连接，而是直接将连接归还给连接池。

数据库连接池涉及到一种设计模式——资源池（Resource Pool）。

JDBC中的数据库连接池使用javax.sql.DataSource来表示。DataSource通常被称为数据源，它包含连接池和连接池管理两部分。习惯上也称DataSource为连接池。
DataSource只是一个接口，该接口由第三方提供。如DBCP数据源、C3P0数据源。

#### DBCP数据源 ####
DBCP是Apache提供的开源连接池。Tomcat的连接池是通过DBCP实现的。
DBCP数据源的应用依赖如下两个Jar包：
	
	commons-dbcp.jar  连接池的实现
	commons-pool.jar  连接池实现的依赖库

#### C3P0数据源 ####

	