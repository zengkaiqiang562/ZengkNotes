## 网络基础知识——计算机网络介绍、通信协议与网络结构模型 ##
**计算机网络**
计算机网络就是把分布在不同地理区域的计算机与专门的外部设备，用通信线路互连成一个网络系统，从而使众多
的计算机可以互相传递信息，共享资源。

**计算机网络分类**
计算机网络通常按照规模大小和延伸范围分类，常见的划分为：局域网（LAN）、城域网（MAN）、广域网（WAN）。Internet可以视为世界上最大的广域网。

**网络通信协议**
计算机网络中实现通信必须有一些约定，这些约定被称为通信协议。

通信协议通常由三部分组成：

	①语义部分：用于决定双方对话的类型；

	②语法部分：用于决定双方对话的格式；

	③变换规则：用于决定通信双方的应答关系。

IP协议：

	IP协议是一种非常重要的通信协议，又称互联网协议。
	是支持网间互联的数据报协议。提供网间连接的完善功能，包括IP数据报规定的互联网络范围内的地址格式。

TCP协议：

	经常与IP协议放在一起的还有TCP协议，即传输控制协议。它规定一种可靠的数据信息传递服务。

虽然IP和TCP这两个协议功能不尽相同，也可以分开单独使用，但它们是在同一个时期作为一个协议来设计的，
并且在功能上也是互补的。因此实际使用中常常把这两个协议统称为TCP/IP协议。

**网络结构模型**
按TCP/IP协议模型，网络通常被分为一个四层模型，这个四层模型和OSI（开放式系统互连参考模式）七层模式
有大致的对应关系。

<table>
<thead><th>OSI分层模型</th><th>TCP/IP分层模型</th></thead>
<tbody>
<tr><td>应用层</td><td rowspan=3>应用层</td></tr>
<tr><td>表示层</td></tr>
<tr><td>会话层</td></tr>
<tr><td>传输层</td><td>传输层</td></tr>
<tr><td>网络层</td><td>网络层</td></tr>
<tr><td>数据链路层</td><td rowspan=2>物理+数据链路</td></tr>
<tr><td>物理层</td></tr>
</tbody>
</table>


## IP地址和端口号 ##

当一个程序需要发送数据时，需要指定目的地的IP地址和端口，只有指定了正确的IP地址和端口号，
计算机网络才能将数据送给该IP地址和端口所对应的程序。

**IP地址**

IP地址用于唯一地标识网络中的一个通信实体，这个通信实体既可以是一台主机，也可以是一台打印机，
或者是路由器的某一个端口。

在基于IP协议网络中传输的数据包，都必须使用IP地址来进行标识。
每个被传输的数据包有一个源IP地址和一个目的IP地址，当该数据包在网络中进行传输时，这两个地址要保持不变，
以确保网络设备总能根据确定的IP地址，将数据包从源通信实体送往指定的目的通信实体。

IP地址是数字型的，IP地址是一个32位的整数。
为了便于记忆，通常把它分为4个8位的二进制数，每8位之间用圆点隔开。
每个8位整数可以转换成一个0~255的十进制整数，因此日常看到的IP地址的格式形如： 222.247.82.201

IP地址被分成了A、B、C、D、E五类，每个类别的网络标识和主机标识各有规则：

	A类： 1.0.0.1 --- 127.255.255.254	

		10.X.X.X是私有地址(私有地址就是在互联网上不使用，而被用在局域网络中的地址)
		127.X.X.X是保留地址，用做循环测试用的。
	

	B类： 128.0.0.1 --- 191.255.255.254

		172.16.0.0 --- 172.31.255.255是私有地址
		169.254.X.X是保留地址。
	

	C类： 192.0.0.1 --- 223.255.255.254	
		192.168.X.X是私有地址
	

	D类： 224.0.0.1 --- 239.255.255.254 	
	
	E类： 240.0.0.1 --- 247.255.255.254	
		

IP地址的组成： IP地址 = 网络号码+主机地址

	A类IP地址:第一段号码为网络号码，剩下的三段号码为本地计算机的号码 : 256*256*256 --- 16777216

	B类IP地址:前二段号码为网络号码，剩下的二段号码为本地计算机的号码 : 256*256 --- 65536

	C类IP地址:前三段号码为网络号码，剩下的一段号码为本地计算机的号码 : 256

		
查看本机IP的命令：
	
	ipconfig


查看网络是否有问题： ping+IP地址 

	ping 127.0.0.1(本机回环地址)


**端口**

IP地址用于唯一地标识网络上的一个通信实体，但一个通信实体可以有多个通信程序同时提供网络服务，
此时还需要使用端口。

端口是一个16位的整数，用于表示数据交给哪个通信程序处理。因此，端口就是应用程序与外界交流的出入口。
这里的端口是指一种抽象的软件结构，包括一些数据结构和I/O缓冲区。

不同应用程序处理不同端口上的数据，同一台机器上不能有两个程序使用同一个端口。

端口号从0~65535，通常将它分为三类：

	公认端口： 从0到1023， 它们紧密绑定一些特定的服务；

	注册端口： 从1024到49151， 它们松散地绑定一些服务。应用程序通常应该使用这个范围内的端口；

	动态和/或私有端口： 从49152到65535， 这些端口是应用程序使用的动态端口，
						应用程序一般不会主动使用这些端口。


## Java中网络相关的API ##

**InetAddress**

Java提供了InetAddress类来代表IP地址，
InetAddress下还有两个子类：Inet4Address、Inet6Address，分别代码表IPv4地址和IPv6地址。

InetAddress类没有构造器，通过静态方法获取InetAddress实例

	public static InetAddress getByName(String host)
		根据主机获取对应的InetAddress对象。

	public static InetAddress getByAddress(byte[] addr)
		根据原始IP地址获取对应的InetAddress对象。

	public static InetAddress getLocalHost()
		获取本机IP地址对应的InetAddress对象。

InetAddress提供的常用方法

	public String getHostName()
		获取此IP地址的主机名

	public String getHostAddress()
		返回此InetAddress实例对应的IP地址字符串。

	public boolean isReachable(int timeout)
		测试是否可以到达该地址。
		因为防火墙和服务器配置可能阻塞请求，使得它在访问某些特定的端口时处于不可到达状态。
		如果可以获取权限，典型的实现是使用ICMP ECHO REQUEST。
		否则它将试图在目标主机的端口7（Echo）上建立连接。


**URLDecoder和URLEncoder**

URLDecoder和URLEncoder用于实现普通字符串和application/x-www-form-urlencoded MIME 字符串
之间的相互转换。

HTTP的get请求包含中文时，在url地址后对应的中文参数会被转换为形如"%XX%XX%XX%XX%XX%XX"的字符串，
这种字符串就被称为是application/x-www-form-urlencoded MIME字符串。

URLDecoder：

	public static String decode(String s, String enc)
		// 将参数s表示的application/x-www-form-urlencoded MIME字符串，通过enc指定的编码集，
			转换为普通字符串。

URLEncoder：

	public static String encode(String s, String enc)
		// 将参数s表示的普通字符串，通过enc指定的编码集，
			转换为application/x-www-form-urlencoded MIME字符串。

仅包含西欧字符的普通字符串和application/x-www-form-urlencoded MIME字符串无须转换，
而包含中文字符的普通字符串则需要转换，
转换方法是每个中文字符占两个字节，每个字节可以转换成两个十六进制的数字 ，
所以每个中文字符将转换为"%XX%XX"的形式。

采用不同的字符集，每个中文字符对应的字节数并不完全相同，所以使用URLEncoder和URLDecoder进行转换时
需要指定字符集。


**URL**
URL（Uniform Resource Locator）对象表示统一资源定位器，它是指向互联网资源的指针。
通常情况下，URL由协议名、主机、端口和资源组成，满足格式：`protocol://host:port/resourcePath`

JDK中还提供了一个URI（Uniform Resource Identifiers）类，表示一个统一资源标识符，
Java中的URI不能用于定位任何资源，它的唯一作用就是解析。
与此对应的是，URL包含一个可打开到达该资源的输入流，可以将URL理解成URI的特例。

URL类的常用API:

	public URL(String spec)
		//使用URL格式的字符串形式创建URL对象。
	
	public URL(String protocol, String host, int port, String file)
		//使用给定的协议、主机、端口和资源创建URL对象。
	
	public URL(String protocol, String host, String file)
		//使用给定协议的默认端口，和给定的资源创建URL对象。
	
	public String getFile()
		//获取该URL的资源名。
	
	public String getHost()
		//获取该URL的主机名。
	
	public String getPath()
		//获取该URL的路径名。
	
	public int getPort()
		//获取该URL的端口号。
	
	public String getProtocol()
		//获取该URL的协议名称。
	
	public String getQuery()
		//获取该URL的查询字符串部分。
	
	public URLConnection openConnection()
		//返回一个URLConnection对象，它代表了与URL所引用的远程对象的连接。
	
	public final InputStream openStream()
		//打开与此URL的连接，并返回一个用于读取该URL资源的InputStream。
			通过调 return openConnection().getInputStream(); 实现。


**URLConnection**
URL的openConnection()方法返回一个URLConnection对象。
URLConnection对象表示应用程序和URL之间的通信连接。
程序可以通过URLConnection实例向该URL发送请求，读取URL引用的资源。
（对于Http协议的实现类可查看`com.squareup.okhttp.internal.huc.HttpURLConnectionImpl`）

通常创建一个和URL的连接，并发送请求，读取此URL引用的资源需如下几个步骤：

	step1. 获取URL对象，通过调用URL对象的openConnection()方法来创建URLConnection对象；
	step2. 设置URLConnection的参数和普通请求属性；

	step3. 建立和远程资源的实际连接。如果只是发送GET方式的请求，则使用URLConnection的connect()方法
			建立和远程资源之间的实际连接即可；
			如果是发送POST方式的请求，则需要调URLConnection的getOutputStream()获取对应的输出流，
			通过输出流向远程资源发送POST方法的请求参数。

	step4. 在远程资源变为可用之后，程序可以访问远程资源的头字段，或通过输入流读取远程资源的数据。
	

在建立和远程资源的实际连接之前，程序可以通过URLConnection提供的如下方法来设置请求头字段：

	public void setAllowUserInteraction(boolean allowuserinteraction)
		//设置URLConnection对象的allowUserInteraction请求头字段值。

	public void setDoInput(boolean doinput)
		//设置URLConnection对象的doInput请求头字段值。

	public void setDoOutput(boolean dooutput)
		//设置URLConnection对象的doOutput请求头字段值。

	public void setIfModifiedSince(long ifmodifiedsince)
		//设置URLConnection对象的ifModifiedSince请求头字段值。

	public void setUseCaches(boolean usecaches)
		//设置URLConnection对象的useCaches请求头字段值。

	public void setRequestProperty(String key, String value)
		//设置通用的头字段。如	setRequestProperty("accept", "*/*")

	public void addRequestProperty(String key, String value)
		//为请求头字段key增加value值。只是在key字段原有值的后面追加新的value值，
			并不会覆盖key字段原来的值。

当远程资源可用之后，程序可以使用URLConnection提供的如下方法获取响应头字段的值，
以及访问远程资源的内容：

	public Object getContent()
		//获取该URLConnection的内容。

	public String getHeaderField(String name)
		//获取指定响应头字段的值。

	public InputStream getInputStream()
		//返回该URLConnection对应的输入流，用于获取URLConnection响应的内容。

	public OutputStream getOutputStream()
		//返回该URLConnection对应的输出流，用于向URLConnection发送请求参数。

除了通过getHeaderFiled(String name)方法获取name指定的响应头字段值之外，
Java还提供了如下方法用于获取常用的响应头字段的值：

	public String getContentEncoding()
		//获取content-encoding响应头字段的值。

	public int getContentLength()
		//获取content-length响应头字段的值。

	public String getContentType()
		//获取content-type响应头字段的值。

	public long getDate()
		//获取date响应头字段的值。

	public long getExpiration()
		//获取expires响应头字段的值。

	public long getLastModified()
		//获取last-modified响应头字段的值。

URLConnection的其他API方法：

	abstract public void connect()
		//用于建立实际的连接，一般在GET方法请求中用到。
			（POST方法请求调了getOutputStream，所以不需要再调connet方法）

	public void setConnectTimeout(int timeout)
		//用于在建立实际连接时，设置的超时时间，单位为ms。
			在设置时间内，没有连接成功，则会报SocketTimeoutException异常

	public void setReadTimeout(int timeout)
		//用于在读取InputStream输入流中的资源时，设置的超时时间，单位为ms。
			在设置时间内，没有读取到资源，则会报SocketTimeoutException异常。

**举例： 使用URLConnection实现GET请求**

	/**
	 * 向指定URL发送GET方法的请求
	 * @param url 发送请求的URL
	 * @param params 请求参数，请求参数应该是name1=value1&name2=value2的形式。
	 * @return URL所代表远程资源的响应
	 */
	public static String sendGet(String url, String params){
		String result = "";
		BufferedReader in = null;
		try{
			String urlName = url + "?" + params;
			URL realUrl = new URL(urlName);
			// 打开和URL之间的连接
			URLConnection conn = realUrl.openConnection();
			// 设置通用的请求属性
			conn.setRequestProperty("accept", "*/*");
			conn.setRequestProperty("connection", "Keep-Alive");
			conn.setRequestProperty("user-agent",
					"Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)");
			// 建立实际的连接
			conn.connect();
			// 获取所有响应头字段
			Map<String, List<String>> map = conn.getHeaderFields();
			// 遍历所有的响应头字段
			for (String key : map.keySet()){
				System.out.println(key + "--->" + map.get(key));
			}
			// 定义BufferedReader输入流来读取URL的响应
			in = new BufferedReader(
					new InputStreamReader(conn.getInputStream()));
			String line;
			while ((line = in.readLine()) != null){
				result += "\n" + line;
			}
		}catch (Exception e){
			System.out.println("发送GET请求出现异常！" + e);
			e.printStackTrace();
		}
		// 使用finally块来关闭输入流
		finally{
			try{
				if (in != null){
					in.close();
				}
			}catch (IOException ex){
				ex.printStackTrace();
			}
		}
		return result;
	}

**举例： 使用URLConnection实现POST请求：**

	使用URLConnection发送POST请求必须设置setDoOutput(true)和setDoInput(true)。
   
	/**
	 * 向指定URL发送POST方法的请求
	 * @param url 发送请求的URL
	 * @param params 请求参数，请求参数应该是name1=value1&name2=value2的形式。
	 * @return URL所代表远程资源的响应
	 */
	public static String sendPost(String url, String params){
		PrintWriter out = null;
		BufferedReader in = null;
		String result = "";
		try{
			URL realUrl = new URL(url);
			// 打开和URL之间的连接
			URLConnection conn = realUrl.openConnection();
			// 设置通用的请求属性
			conn.setRequestProperty("accept", "*/*");
			conn.setRequestProperty("connection", "Keep-Alive");
			conn.setRequestProperty("user-agent",
					"Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)");
			// 发送POST请求必须设置如下两行
			conn.setDoOutput(true);
			conn.setDoInput(true);
			// 获取URLConnection对象对应的输出流
			out = new PrintWriter(conn.getOutputStream());
			// 发送请求参数
			out.print(params);  // ②
			// flush输出流的缓冲
			out.flush();
			// 定义BufferedReader输入流来读取URL的响应
			in = new BufferedReader(
					new InputStreamReader(conn.getInputStream()));
			String line;
			while ((line = in.readLine()) != null){
				result += "\n" + line;
			}
		}catch (Exception e){
			System.out.println("发送POST请求出现异常！" + e);
			e.printStackTrace();
		}
		// 使用finally块来关闭输出流、输入流
		finally{
			try{
				if (out != null){
					out.close();
				}
				if (in != null){
					in.close();
				}
			}
			catch (IOException ex){
				ex.printStackTrace();
			}
		}
		return result;
	}


**HttpURLConnection**
（实现类可查看`com.squareup.okhttp.internal.huc.HttpURLConnectionImpl`）

HttpURLConnection是URLConnection的子类。HttpURLConnection在URLConnection的基础上做了进一步改进，增加了一些用于操作HTTP资源的方法。

HttpURLConnection一些特有的方法如下：

	public int getResponseCode()
		//获取服务器的响应码。

	public String getResponseMessage()
		//获取服务器的响应消息。

	public void setRequestMethod(String method)
		//设置发送HTTP请求的方式。

	public String getRequestMethod()
		//获取发送HTTP请求的方式。


//TODO
举例： 使用HttpURLConnection实现文件下载


----------

未完待续......