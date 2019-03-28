## 代码中字符串不同平台换行符的表示方法： ##
	1. Windows平台——`\r\n`
	2. UNIX/Linux/BSD平台——`\n`

## Java中的IO流体系分类

| 分类 | 字节输入流 | 字节输出流 | 字符输入流 | 字符输出流 |
|----|----|----|----|----|
| 抽象基类 | *InputStream* | *OutputStream* | *Reader* | *Writer* |
| 访问文件 | **FileInputStream** | **FileOutputStream** | **FileReader** | **FileWriter** |
| 访问数组 | **ByteArrayInputStream** | **ByteArrayOutputStream** | **CharArrayReader** | **CharArrayWriter** |
| 访问管道 | **PipedInputStream** | **PipedOutputStream** | **PipedReader** | **PipedWriter** |
| 访问字符串 |      |      | **StringReader** | **StringWriter** |
| 缓冲流 | BufferedInputStream | BufferedOutputStream | BufferedReader | BufferedWriter |
| 转换流 |      |      | InputStreamReader | OutputStreamWriter |
| 对象流 | ObjectInputStream | ObjectOutputStream |      |     |
|----|----|----|----|----|
| 抽象基类 | *FilterInputStream* | *FilterOutputStream* | *FilterReader* | *FilterWriter* |
| 打印流 |  | PrintStream |  | PrintWriter |
| 推回输入流 | PushbackInputStream |  | PushbackReader |  |
| 特殊流 | DataInputStream | DataOutputStream |  |  |

- 粗体字标出的类代表节点流，必须直接与指定的物理节点关联(比如File对象表示的文件，内存中的字符串或数组等)

----------

上图显示了Java的IO流体系中常用的流分类。Java为了更好的设计，将IO流按功能分成了很多类，而每类中又分别提供了字节流和字符流，字节流和字符流中又分别提供了输入流和输出流，所以导致整个IO体系显得较为复杂。

### 一、 字节流和字符流： ###

- 字节流主要以InputStream和OutputStream作为基类；字符流主要以Reader和Writer作为基类。
- 字节流和字符流的用法几乎是一样的，区别在于字节流和字符流所操作的数据单元不同——节流操作的数据单元是8位的字节，而字符流操作的数据单元是16位的字符（2个字节）。

### 二、 输入流和输出流： ###
- 输入流主要以InputStream和Reader作为基类；输出流主要以OutputStream和Writer作为基类；
- 输入流只能从中读取数据，不能向其中写入数据；
- 输出流只能向其中写入数据，不能从中读取数据；
- 从不同的角度看，输入流和输出流的概念是相对的。写代码时，一般参照程序运行所在的内存开划分。流入内存的对程序来说是输入流，流出内存的对程序来说是输出流。

### 三、 节点流和处理流： ###
- 使用节点流进行输入/输出时，程序直接连接到实际的数据源，和实际的输入/输出节点连接。节点流也被称为低级流；
- 处理流则用于对一个已存在的流进行连接或封装，通过封装后的流来实现数据读/写功能。处理流也被称为高级流。又因为Java使用处理流来包装节点流是一种典型的装饰器设计模式，所以处理流也被称为包装流。

	>Java的处理流模型体现了Java输入/输出流设计的灵活性。处理流的功能主要体现在以下两个方面：
	>>1. 性能的提高：主要以增加缓冲的方式来提高输入/输出的效率；
	>>2. 操作的便捷：处理流可能提供了一系列便捷的方法来一次输入/输出大批量的内容。
	>
	>通过使用处理流，Java程序只要将节点流包装成处理流，就可以使用相同的输入/输出代码来读写不同的输入/输出设备的数据。

## IO流中常用的API
### InputStream（字节输入流的基类）： ###
1. `public abstract int read() throws IOException;`:抽象方法。从输入流读取单个字节，返回所读取的字节数据(字节数据可直接转换为int类型)。返回-1表示到了输入流的结束点。

2. `public int read(byte b[], int off, int len) throws IOException`：从输入流中最多读取len个字节的数据，并将其存储在数组b中，放入数组b时，并不是从数组起点开始，而是从off位置开始，返回实际(实际长度可能小于len)读取的字节数。返回-1表示到了输入流的结束点。

3. `public int read(byte b[]) throws IOException`：从输入流中最多读取b.length个字节的数据，并将其存储在数组b中，返回实际读取的字节数。相当于调用`read(b, 0, b.length)`。返回-1表示到了输入流的结束点。

### Reader(字符输入流的基类)：
1. `public int read() throws IOException`:从输入流读取单个字符，返回所读取的字符数据(字符数据可直接转换为int类型)。返回-1表示到了输入流的结束点。

2. `abstract public int read(char cbuf[], int off, int len) throws IOException;`：抽象方法。从输入流中最多读取len个字符的数据，并将其存储在数组b中，放入数组b时，并不是从数组起点开始，而是从off位置开始，返回实际(实际长度可能小于len)读取的字符数。返回-1表示到了输入流的结束点。

3. `public int read(char cbuf[]) throws IOException`：从输入流中最多读取b.length个字符的数据，并将其存储在数组b中，返回实际读取的字符数。相当于调用`read(cbuf, 0, cbuf.length)`。返回-1表示到了输入流的结束点。

> - InputStream和Reader都支持如下几个方法来移动记录指针
>>1. `void mark(int readAheadLimit)`: 在记录指针当前位置记录一个标记mark。
>>2. `boolean markSupported()`: 判断此输入流是否支持mark()操作，即是否支持记录标记。
>>3. `reset()`:将此流的记录指针重新定位到上一次记录标记的位置。
>>4. `long skip(long n)`: 记录指针向前移动n个字节/字符。


>- Java的IO流执行输入时，最后需要调close()方法关闭IO流，为了保证执行关闭代码，使流的物理资源被回收，close()方法最好放在finally代码块中。

### OutputStream（字节输出流的基类）
1. `public abstract void write(int b) throws IOException;`：将指定的字节输出到输出流中，此处c表示字节。

2. `public void write(byte b[], int off, int len) throws IOException`：将字节数组中从off位置开始，长度为len的字节输出到输出流中。

3. `public void write(byte b[]) throws IOException`：将字节数组中的数据输出到指定输出流中。相当于调了`write(b, 0, b.length)`。

### Writer（字符输出流的基类）
1. `public void write(int c) throws IOException`：将指定的字符输出到输出流中，此处c表示字符。

2. `abstract public void write(char cbuf[], int off, int len) throws IOException;`：将字符数组中从off位置开始，长度为len的字符输出到输出流中。

3. `public void write(char cbuf[]) throws IOException`：将字符数组中的数据输出到指定输出流中。相当于调了`write(cbuf, 0, cbuf.length)`

4. `public void write(String str, int off, int len) throws IOException`：将str字符串中从off位置开始，长度为len的字符输出到指定输出流中。

5. `public void write(String str) throws IOException`：将str字符串中所有的字符输出到指定输出流中。相当于调了`write(str, 0, str.len)`。

>- 以OutputStream和Writer为基类的输出流还多了`flush()`方法，对于存在缓冲区的输出流，此方法用于将输出缓冲区中的数据刷新到物理节点中去。

>- Java的IO流执行输出时，最后同样需要调close()方法关闭IO流。关闭输出流除了可以保证流的物理资源被回收之外，可能还可以将输出流缓冲区中的数据flush到物理节点里（因为在执行close()方法之前，自动执行输出流的flush()方法）。


### 处理流 PrintStream ###
处理流PrintStream通过其构造函数 `PrintStream(OutputStream out)` 可以用来包装一个OutputStream。

通常如果需要输出文本内容，都可以将输出流包装成PrintStream后进行输出

System.out的类型就是PrintStream

	
### 转换流 InputStreamReader 、 OutputStreamWriter ###
只有将字节流转换成字符流的转换流。之所以要转换，是为了方便对IO流的操作，而字符流比字节流操作方便，所以应该讲字节流转成字符流操作。

通过转换流提供的构造函数可以将一个字节流转换成字符流

	try {
		//System.in是标准输入，即键盘输入
		InputStreamReader reader = new InputStreamReader(System.in);
		BufferedReader br = new BufferedReader(reader);

		while((line = br.readLine()) != null) {
			System.out.println(line);
		}

	} catch(IOException e) {
		e.printStackTrace();
	}

### 缓冲流 BufferedReader ###
BufferedReader具有一个readLine()方法，可以非常方便地一次读入一行内容，所以经常把读取文本内容的输入流包装成BufferedReader。
如上例中的读取标准输入System.in的内容。

### 重定向标准输入/输出 ###
System类提供setErr、SetIn、SetOut依次用于重定向标准错误流、标准输入流、标准输出流。

重定向标准输出：

	try {

		PrintStream ps = new PrintStream(new FileOutputStream("out.txt"));
		System.setOut(ps);
		System.out.println("普通字符串");

	} catch (IOException e) {
		e.printStackTrace();
	}

重定向标准输入：

	try {
		FileInputStream fis = new FileInputStream("RedirectIn.java");
		System.setIn(fis);

		Scanner sc = new Scanner(System.in);

		sc.useDelimiter("\n");

		while(sc.hasNext()) {
			System.out.println(sc.next());
		}
	
	} catch (IOException e) {
		e.printStackTrace();
	}

### RandomAccessFile ###
既可以读取文件内容，也可以向文件输出数据；
支持随机访问的方式，程序可以直接跳转到文件的任意位置来读写数据

当只需要读取文件部分内容，而不需要从头到尾读取文件时，可以采用RandomAccessFile。

因为RandomAccessFile允许自由定位文件记录指针，所以RandomAccessFile可以从非起始位置进行输出。
所以当程序需要向已存在的文件后追加内容时，可以采用RandomAccessFile。

RandomAccessFile提供如下方法操作记录指针：

	long getFilePointer()  返回文件记录指针的当前位置。
	void seek(long pos)  将文件记录指针定位到pos位置。

RandomAccessFile构造函数中的mode参数用于指定访问模式：

	r  以只读方式打开指定文件。此时RandomAccessFile若向此文件输出数据，则会抛出IOException异常。

	rw  以读、写方式打开指定文件。如果文件不存在，则尝试创建该文件。

	rws  以读、写方式打开指定文件。相对于"rw"模式，还要求对文件的内容或元数据的每个更新都同步写入到
		底层存储设备。

	rwd  以读、写方式打开指定文件。相对于"rw"模式，还要求对文件内容的每个更新都同步写入到底层存储设
		备。


使用RandomAccessFile向文件的指定位置插入内容时，RandomAccessFile输出到文件的内容会覆盖掉文件中原有的内容。此时，需要将插入点之后的内容读入缓冲区，等把需要插入的数据写入文件后，再将缓冲区的内容追加到文件后面。

	public static void insert(String fileName, long pos, String insertContent) 
		throws IOException {

		File tmp = File.createTempFile("tmp", null);
		tmp.deleteOnExit();

		RandomAccessFile raf = new RandomAccessFile(fileName, "rw");

		FileOutputStream tmpOut = new FileOutputStream(tmp);
		FileInputStream tmpIn = new FileInputStream(tmp);

		//将插入点之后的内容读入缓冲区，并保存在临时文件中。
		
		raf.seek(pos);

		byte[] bbuf = new byte[64];

		int hasRead = 0;

		while ((length = raf.read(bbuf)) > 0) {
			tmpOut.write(bbuf, 0, length);
		}

	 	//从插入点开始向文件写入内容

		raf.seek(pos);

		raf.write(insertContent.getBytes());

		//将临时文件中的内容写入到文件中

		while((length = tmpIn.read(bbuf)) > 0) {
			raf.write(bbuf, 0, length);
		}
	}

#### RandomAccessFile 实现多线程断点下载 ####
多线程断点下载可通过RandomAccessFile实现：
step1. 下载开始时，建立两个文件。其中一个文件是与被下载文件大小相同的空文件；另一个是记录文件指针的位置文件。

step2. 在多线程下载过程中，每个线程中都使用RandomAccessFile将网络数据写入到空文件中，每写入一些数据后，就将RandomAccessFile的当前文件指针的位置写入到记录文件指针的文件中。

step3. 网络断开后，再次开始下载时，每个RandomAccessFile都根据记录文件指针的文件中的记录位置继续向下写数据。

----------
//TODO（处理流详解、转换流、重定向标准输入/输出、RandomAccessFile介绍、对象序列化、NIO介绍、Properties介绍）
未完待续 。。。。。