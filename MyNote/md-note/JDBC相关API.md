JDK Version： 1.8

java.sql.DriverManager
	public static Connection getConnection(String url, String user, String password) throws SQLException

java.sql.Connection
	Statement createStatement() throws SQLException;
	PreparedStatement prepareStatement(String sql) throws SQLException;
	CallableStatement prepareCall(String sql) throws SQLException;

	void setAutoCommit(boolean autoCommit) throws SQLException;
	boolean getAutoCommit() throws SQLException;
	
	void commit() throws SQLException;
	void rollback() throws SQLException;

	Savepoint setSavepoint(String name) throws SQLException;
	Savepoint setSavepoint() throws SQLException;
	void releaseSavepoint(Savepoint savepoint) throws SQLException;
	void rollback(Savepoint savepoint) throws SQLException;

	void close() throws SQLException;

java.sql.Statement
	ResultSet executeQuery(String sql) throws SQLException;
	int executeUpdate(String sql) throws SQLException;
	boolean execute(String sql) throws SQLException;

	void close() throws SQLException;

java.sql.PreparedStatement
	public interface PreparedStatement extends Statement

	void setXxx(int parameterIndex, Xxx value)  其中Xxx表示数据类型，

java.sql.ResultSet
	boolean absolute( int row ) throws SQLException;
	void beforeFirst() throws SQLException;
	boolean previous() throws SQLException;
	boolean next() throws SQLException;
	boolean last() throws SQLException;
	void afterLast() throws SQLException;

	boolean getXxx(int columnIndex) throws SQLException;
	boolean getXxx(String columnLabel) throws SQLException;

	public <T> T getObject(int columnIndex, Class<T> type) throws SQLException;
	public <T> T getObject(String columnLabel, Class<T> type) throws SQLException;

	void close() throws SQLException;