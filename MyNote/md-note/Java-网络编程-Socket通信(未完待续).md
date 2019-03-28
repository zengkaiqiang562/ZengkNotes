- Socket通信基于TCP/IP协议。TCP/IP通信协议是一种可靠的网络协议，它**在通信的两端各建立一个Socket，从而在通信的两端之间形成网络虚拟链路**。一旦建立了虚拟的网络链路，两端的程序就可以通过网络虚拟链路进行通信。
- Java对基于TCP/IP协议的网络通信提供了良好的封装，Java使用Socket对象来代表两端的通信端口，并通过Socket产生IO流来进行网络通信。

## TCP/IP协议基础介绍
**1. IP协议**
- IP协议是Internet上使用的一个关键协议，全称是Internet Protocol，即Internet协议，简称IP协议。
- 通过使用IP协议，从而使Internet成为一个允许连接不同类型的计算机和不同操作系统的网络。
- IP协议保证计算机能发送和接收分组数据。IP协议负责将消息从一个主机传送到另一个主机，消息在传送过程中被分割成一个个的小包。

IP协议只是保证了计算机之间可以发送和接收数据，但IP协议并不能解决数据分组在传输过程中可能出现的异常情况。此时需要通过TCP协议来提供可靠并且无差错的通信服务。

**2. TCP协议**
- TCP协议被称作一种端对端协议。当一台计算机需要与另一台远程计算机连接时，TCP协议会让它们建立一个连接，即用于发送和接收数据的虚拟链路。
- TCP协议负责收集数据包，并将它们按适当的次序传送，接收端收到后再将它们正确地还原。
- TCP协议使用重发机制： 当一个通信实体发送一个消息给另一个通信实体后，需收到另一个通信实体的确认信息，如果没有收到另一个通信实体的确认信息，则会再从发刚才发送的信息。
- 通过TCP协议的重发机制，TCP协议向应用程序提供了可靠的通信连接，使它能够自动适应网上的各种变化，即使在Internet暂时出现堵塞的情况下，TCP也能保证通信的可靠性。


虽然IP和TCP这两个协议功能不尽相同，也可以分开单独使用，但它们是在同一个时期作为一个协议来设计的，并且在功能上也是互补的。只有两者结合起来，才能保证Internet在复杂的环境下正常运行。凡是要连接到Internet的计算机，都必须同时安装和使用这两个协议，因此实际使用中常常把这两个协议统称为TCP/IP协议。


## Java中Socket通信相关的API介绍

### 一、ServerSocket

- 在两个通信实体没有建立虚拟链路之前，必须有一个通信实体先做出“主动姿态”，主动接收来自其他通信实体的连接请求。可以将此做出“主动姿态”的通信实体看做是TCP服务器端。
- Java中能接收其他通信实体连接请求的类是ServerSocket。ServerSocket对象用于监听来自客户端的Socket连接，如果没有连接，ServerSocket对象将一直处于等待状态。

`public ServerSocket(int port)`：使用指定的端口port来创建ServerSocket。为了避免与其他应用程序的通用端口冲突，建议使用1024以上的端口。  
`public ServerSocket(int port, int backlog)`： 指定连接队列中最大能允许多少个来自客户端的Socket连接请求。没有指定backlog或backlog小于1，则使用默认值（Java1.7的默认值是50）。
`public ServerSocket(int port, int backlog, InetAddress bindAddr)`： 在机器存在多个IP地址的情况下，允许通过localAddr参数来指定将ServerSocket绑定到指定的IP地址。

`public Socket accept()`： 监听来自客户端的连接请求。如果接收到一个客户端Socket的连接请求，该方法将返回一个与客户端Socket对应的Socket；否则该方法将一直处于等待状态，线程也被阻塞。当调用此accept方法返回一个Socket对象后，通信的两端之间就形成网络虚拟链路，于是就可以通过两端的Socket对象产生的IO流，经过网络虚拟链路进行通信了。

`public void close()`： 当ServerSocket使用完毕后，通过该close方法来关闭ServerSocket。调用close方法后，accept中阻塞的线程将抛出SocketException异常：`throw new SocketException("Socket is closed");`。并且与客户端建立的Socket连接也会被关闭。

>通常情况下，服务器不会只接受一个客户端请求，而应该不断地接收来自客户端的所有请求，所以Java程序通常会通过循环(比如一个死循环)不断地调用ServerSocket的accept()方法。


### 二、Socket

客户端通过创建一个Socket对象，连接到服务器。

`public Socket(String host, int port)` 
`public Socket(InetAddress address, int port)`
- 使用本地主机默认的IP地址和系统动态分配的端口， 创建一个Socket，并使该Socket对象连接到指定远程主机和远程端口的服务器程序（连接成功后会通过服务器端ServerSocket的accept()方法，在服务器端返回一个与此客户端Socket对应的服务器端Socket对象）。
- 远程主机的IP地址可以使用字符串形式的host表示，也可以封装成一个InetAddress对象传递进来。

`public Socket(String host, int port, InetAddress localAddr, int localPort)`
`public Socket(InetAddress address, int port, InetAddress localAddr, int localPort)`
- 使用指定的本地IP地址localAddress，和指定的本地端口localPort，创建一个Socket，并使该Socket对象连接到指定远程主机和远程端口的服务器程序（连接成功后会通过服务器端ServerSocket的accept()方法，在服务器端返回一个与此客户端Socket对应的服务器端Socket对象）。
- 远程主机的IP地址可以使用字符串形式的host表示，也可以封装成一个InetAddress对象传递进来。

`public Socket()`： 创建一个未连接的Socket对象。如果想将其与服务器端的Socket连接， 还需调connect方法。
`public void connect(SocketAddress endpoint, int timeout)`： 连接到endpoint指定的远程服务器端的Socket程序。设置timeout指定连接超时时间，单位ms。timeou设置为0表示没有限制超时时间。
- 通过SocketAddress的子类InetSocketAddress来封装远程主机和远程端口，相关构造方法是`InetSocketAddress(String hostname, int port)`或`InetSocketAddress(InetAddress addr, int port)`

`public synchronized void setSoTimeout(int timeout)`： 从Socket中读取输入流设置的超时时间，单位是ms。

`public InputStream getInputStream()`： 返回该Socket对象对应的输入流，让程序通过该输入流从远端Socket中取出数据。
`public OutputStream getOutputStream()`： 返回该Socket对象对应的输出流，让程序通过该输出流向远端Socket中写入数据。
- API只是返回输入/输出节点流的基类，所以不管底层的IO流到底是什么节点流，程序都可以将其包装成处理流，从而提供更多方便的处理。


`public synchronized void close()`： 关闭该Socket对象，
- Socket中，当前操作IO流的阻塞线程将会报SocketException异常；
- 调用此close方法后的Socket对象，无法再进行连接。若还需连接，则要另外创建一个新的Socket对象；
- 调用此close方法，Socket中的InputStream和OutputStream都会被关闭。

`public boolean isClosed()`： 判断此Socket对象是否调了close方法做了关闭处理。
- 如果只是调shutdownInput和/或shutdownOutput关闭了输入/输出流，而没有调close方法关闭Socket，isClosed仍然返回true。

`public void shutdownInput()`： 关闭该Socket对象的输入流，程序还可以通过该Socket的输出流输出数据。
`public void shutdownOutput()`： 关闭该Socket对象的输出流，程序还可以通过该Socket的输入流读取数据。
`public boolean isInputShutdown()`： 判断Socket对象的输入流是否关闭。
`public boolean isOutputShutdown()`： 判断Socket对象的输出流是否关闭。
- 即使对一个Socket对象先后调用shutdownInput和shutdownOutput,该Socket对象仍然没有被关闭，只不过此时的Socket对象既不能输入数据，也不能输出数据。
- 当调用了Socket对象的shutdownInput或shutdownOutput关闭输入流或输出流后，该Socket对象无法再次打开输入流或输出流。因此这种做法不适合保持持久通信状态的交互式应用。

## 使用Java提供的Socket通信相关API实现多人聊天功能

//TODO （加入多线程、在通信数据中加入协议字符区分数据信息、相关源码）
未完待续......
