## java.net.InetAddress ##

	public class InetAddress implements java.io.Serializable

	public static InetAddress getByName(String host) throws UnknownHostException

	public static InetAddress getByAddress(byte[] addr) throws UnknownHostException

	public static InetAddress getLocalHost() throws UnknownHostException

	public String getHostAddress()

	public byte[] getAddress()

	public String getHostName()

	public boolean isReachable(int timeout) throws IOException

## java.net.URLDecoder ##

	public class URLDecoder

	public static String decode(String s, String enc) throws UnsupportedEncodingException

## java.net.URLEncoder ##

	public class URLEncoder

	public static String encode(String s, String enc) throws UnsupportedEncodingException

## java.net.URL ##

	public final class URL implements java.io.Serializable

	public URL(String spec) 
			throws MalformedURLException

	public URL(String protocol, String host, String file)
        	throws MalformedURLException

	public URL(String protocol, String host, int port, String file)
			throws MalformedURLException

	public String getProtocol()

	public String getAuthority()

	public String getHost()

	public int getPort()

	public String getPath()

	public String getFile()

	public String getQuery()

	public URLConnection openConnection() throws java.io.IOException
		//创建一个与URL代表的远程资源之间的连接实体类URLConnection对象。调用此方法，仅仅是创建
			连接，并没有打开连接。只有调用了URLConnection的connect()方法后，才是真正地打开连接，
			只有在打开连接，与实际网络建立起连接关系之后，才能访问到远程资源。

	public final InputStream openStream() throws java.io.IOException {
        return openConnection().getInputStream();
    }

 * @see     java.net.URL#openConnection()
 * @see     java.net.URLConnection#connect()

 *   <li>{@code setAllowUserInteraction}
 *   <li>{@code setDoInput}
 *   <li>{@code setDoOutput}
 *   <li>{@code setIfModifiedSince}
 *   <li>{@code setUseCaches}
 *   java.net.URLConnection#setDefaultUseCaches(boolean)
 *   java.net.URLConnection#setRequestProperty(java.lang.String, java.lang.String)

 *   <li>{@code getContent}
 *   <li>{@code getHeaderField}
 *   <li>{@code getInputStream}
 *   <li>{@code getOutputStream}

 *   <li>{@code getContentEncoding}
 *   <li>{@code getContentLength}
 *   <li>{@code getContentType}
 *   <li>{@code getDate}
 *   <li>{@code getExpiration}
 *   <li>{@code getLastModifed}

## java.net.URLConnection ##
URLConnection只是一个抽象类，对于Http协议使用子类HttpURLConnection，HttpConnection也是个抽象类，
在Android中，使用了OkHttp之后，HttpURLConnection的子类就是
`com.squareup.okhttp.internal.huc.HttpURLConnectionImpl` 
(参考源码地址：https://android.googlesource.com/platform/external/okhttp/+/master)

	java.net.URLConnection	

	---------以下请求参数必须要在打开连接之前设置------------

	public void setAllowUserInteraction(boolean allowuserinteraction)

	public void setDoInput(boolean doinput)
		//设置为true，表示应用程序企图以IO流形式读取服务器的数据（如执行getInputStream方法）

	public void setDoOutput(boolean dooutput)
		//设置为true，表示应用程序企图以IO流形式向服务器发送数据（如执行getOuputStream方法）

	public void setIfModifiedSince(long ifmodifiedsince)
		//设置请求首部字段"If-Modified-Since"，
		//ifmodifiedsince： A nonzero value gives a time as the number of milliseconds 
							since January 1, 1970, GMT（即年月日时分秒的毫秒表示形式）

	public void setUseCaches(boolean usecaches)
	public void setDefaultUseCaches(boolean defaultusecaches)
		//是否使用缓存，true为使用缓存，false为不使用缓存。
		//如果没有调用setUseCaches方法，则默认的是否使用缓存的标记由setDefaultUseCaches方法指定，
			如果setDefaultUseCaches也没有调用，那么默认使用缓存

	public void setRequestProperty(String key, String value)
		//设置key表示的请求首部字段，当字段key已存在时，对应的原value值被参数value值覆盖

	public void addRequestProperty(String key, String value)
		//设置key表示的请求首部字段，即使字段key已存在，参数value值也不会覆盖掉原value值。
			（一个key值对应多个value值的场景）

	----------------------------------------
	---------打开连接之后，向服务器发送请求，和 获取服务器响应的数据---------
	abstract public void connect() throws IOException;
		//打开连接，与实际网络建立起连接关系

	public OutputStream getOutputStream() throws IOException
		//以IO流形式发送请求参数，在此之前需要执行 setDoOutput(true)
			调用此方法之前，不必调用connect()方法打开连接。因为getOutputStream方法中已经包含了
			打开连接的代码逻辑

	public InputStream getInputStream() throws IOException
		//以IO流形式获取服务器响应的数据，在此之前需要执行 setDoInput(true)
			调用此方法之前，不必调用connect()方法打开连接。因为getInputStream方法中已经包含了
			打开连接的代码逻辑

	public Object getContent() throws IOException {

        // Must call getInputStream before GetHeaderField gets called
        // so that FileNotFoundException has a chance to be thrown up
        // from here without being caught.

        getInputStream();
        return getContentHandler().getContent(this);
    }
		//获取响应实体，响应实体类型由 getContentType()决定

	public String getHeaderField(String name)
		//获取服务器返回的响应首部字段或实体首部字段name的值，
			当字段name对应有多个值时，仅返回最后设置的那个值。
			如果要获取字段name的所有值，需调用getHeaderFields方法

	public Map<String,List<String>> getHeaderFields()
		//返回服务器返回的所有响应首部字段或实体首部字段，
			Map中的key表示字段名称，value为List<String>集合，
			当字段包含多个值时，集合中的元素就会有多个；当字段只对应有一个值时，集合中就只有一个元素

	public String getContentEncoding() {
        return getHeaderField("content-encoding");
    }

	public String getContentType() {
        return getHeaderField("content-type");
    }

	public int getContentLength() {

        long l = getContentLengthLong();

        if (l > Integer.MAX_VALUE)
            return -1;

        return (int) l;
    }

	public long getContentLengthLong() {
        return getHeaderFieldLong("content-length", -1); // -1是默认值
    }

	public long getDate() {
        return getHeaderFieldDate("date", 0);
    }

	public long getExpiration() {
        return getHeaderFieldDate("expires", 0);
    }

	public long getLastModified() {
        return getHeaderFieldDate("last-modified", 0);
    }
