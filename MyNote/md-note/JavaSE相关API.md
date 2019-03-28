JDK Version 1.8

### java.lang.System ###

	public final static InputStream in = null;
	public final static PrintStream out = null;
	public final static PrintStream err = null;

	public static void setIn(InputStream in)
	public static void setOut(PrintStream out)
	public static void setErr(PrintStream err)

	public static native long currentTimeMillis();

	public static native void arraycopy(Object src,  int  srcPos,
                                        Object dest, int destPos,
                                        int length);

	public static Properties getProperties()
	public static void setProperties(Properties props)

	public static String getProperty(String key)
	public static String getProperty(String key, String def)
	public static String setProperty(String key, String value)
	public static String clearProperty(String key)
	
	public static String lineSeparator()

	public static void exit(int status)  // status为0表示正常退出，非0不正常退出

	public static void gc()

	public static void runFinalization()

	public static void load(String filename)

	public static void loadLibrary(String libname)

### java.lang.Runtime ###
	
	public static Runtime getRuntime()

	public void exit(int status)

	public Process exec(String command) throws IOException

	public Process exec(String command, String[] envp) throws IOException

	public Process exec(String command, String[] envp, File dir) throws IOException

	public Process exec(String cmdarray[]) throws IOException

	public Process exec(String[] cmdarray, String[] envp, File dir) throws IOException

	public native int availableProcessors();

	public native long freeMemory();

	public native long totalMemory();

	public native long maxMemory();

	public native void gc();

	public void runFinalization()

### java.util.Properties  （处理键值对文件） ###

	public Properties()
	
	public synchronized Object setProperty(String key, String value)

	public synchronized void load(Reader reader) throws IOException
	public synchronized void load(InputStream inStream) throws IOException
	public void store(Writer writer, String comments) throws IOException
	public void store(OutputStream out, String comments) throws IOException

	public synchronized void loadFromXML(InputStream in) throws IOException, InvalidPropertiesFormatException
	public void storeToXML(OutputStream os, String comment, String encoding) throws IOException

	public String getProperty(String key)
	public String getProperty(String key, String defaultValue)

	public Enumeration<?> propertyNames()
	public Set<String> stringPropertyNames()


### java.lang.Class<T> ###

	public static Class<?> forName(String className) throws ClassNotFoundException

### java.util.Enumeration<E> ###

	boolean hasMoreElements();
	E nextElement();

### java.net.URLDecoder ###

	public static String decode(String s, String enc)

### java.net.URLEncoder ###

	public static String encode(String s, String enc)

### java.util.UUID ###

	public final class UUID implements java.io.Serializable, Comparable<UUID>

	public UUID(long mostSigBits, long leastSigBits)	

	public static UUID randomUUID()

	public static UUID nameUUIDFromBytes(byte[] name)

	public static UUID fromString(String name)

	public int version()

	public long timestamp()

	public String toString()

	public int compareTo(UUID val)

### java.nio.charset.Charset ###

	public static Charset forName(String charsetName)

	public static Charset defaultCharset()

### java.util.Scanner ###

	public final class Scanner implements Iterator<String>, Closeable

	public Scanner(InputStream source)
	public Scanner(InputStream source, String charsetName)

	public Scanner(File source) throws FileNotFoundException
	public Scanner(File source, String charsetName) throws FileNotFoundException

	public Scanner(Path source) throws IOException
	public Scanner(Path source, String charsetName) throws IOException

	public Scanner(String source)

	public Scanner useDelimiter(Pattern pattern)
	public Scanner useDelimiter(String pattern)

	public Pattern delimiter()

	public boolean hasNext()

	public String next()

	public void remove()

	public void close()

	

### java.util.Iterator<E> ###

	boolean hasNext();

	E next();

	default void remove()

# IO 相关 #

## java.io.File ##

	public class File implements Serializable, Comparable<File>

	public File(String pathname)

	public File(String parent, String child)

	public File(File parent, String child)

	public File(URI uri)

	public String getName()

	public String getParent()

	public File getParentFile()

	public String getPath()

	public boolean isAbsolute()

	public String getAbsolutePath()

	public URI toURI()

	public boolean canRead()

	public boolean canWrite()

	public boolean exists()

	public boolean isDirectory()

	public boolean isFile()

	public boolean isHidden()

	public long lastModified()

	public long length()

	public boolean createNewFile()

	public boolean delete()

	public void deleteOnExit()  //文件在JVM退出时被删除

	public String[] list()

	public String[] list(FilenameFilter filter)

	public File[] listFiles()

	public File[] listFiles(FilenameFilter filter)

	public File[] listFiles(FileFilter filter)

	public boolean mkdir()

	public boolean mkdirs()

	public boolean renameTo(File dest)

	public boolean setLastModified(long time)

	public boolean setReadOnly()

	public boolean setWritable(boolean writable, boolean ownerOnly)

	public boolean setWritable(boolean writable)

	public boolean setReadable(boolean readable, boolean ownerOnly)

	public boolean setReadable(boolean readable)

	public boolean canExecute()

	public boolean setExecutable(boolean executable)

	public static File[] listRoots()

	public long getTotalSpace()

	public long getFreeSpace()

	public long getUsableSpace()

	public static File createTempFile(String prefix, String suffix, File directory)
        throws IOException  

	public static File createTempFile(String prefix, String suffix)  
        throws IOException

	public int compareTo(File pathname)

## java.io.FilenameFilter ##

	public interface FilenameFilter

	boolean accept(File dir, String name);

## java.io.FileFilter ##

	public interface FileFilter

	boolean accept(File pathname);

## java.io.InputStream ##

	public abstract class InputStream implements Closeable

	public abstract int read() throws IOException;

	public int read(byte b[]) throws IOException

	public int read(byte b[], int off, int len) throws IOException

	public long skip(long n) throws IOException

	public int available() throws IOException

	public synchronized void mark(int readlimit)

	public synchronized void reset() throws IOException

	public boolean markSupported()

	public void close() throws IOException

## java.io.Reader ##

	public abstract class Reader implements Readable, Closeable

	public int read() throws IOException

	public int read(char cbuf[]) throws IOException

	abstract public int read(char cbuf[], int off, int len) throws IOException;

	public long skip(long n) throws IOException

	public boolean ready() throws IOException

	public boolean markSupported()
	
	public void mark(int readAheadLimit) throws IOException

	public void reset() throws IOException

	abstract public void close() throws IOException;

## java.io.OutputStream ##

	public abstract class OutputStream implements Closeable, Flushable

	public abstract void write(int b) throws IOException;

	public void write(byte b[]) throws IOException

	public void write(byte b[], int off, int len) throws IOException

	public void flush() throws IOException

	public void close() throws IOException

## java.io.Writer ##

	public abstract class Writer implements Appendable, Closeable, Flushable

	public void write(int c) throws IOException

	public void write(char cbuf[]) throws IOException

	abstract public void write(char cbuf[], int off, int len) throws IOException;

	public void write(String str) throws IOException

	public void write(String str, int off, int len) throws IOException

	public Writer append(CharSequence csq) throws IOException

	public Writer append(CharSequence csq, int start, int end) throws IOException

	public Writer append(char c) throws IOException

	abstract public void flush() throws IOException;

	abstract public void close() throws IOException;

## java.io.FileInputStream ##

	public class FileInputStream extends InputStream

	public FileInputStream(String name) throws FileNotFoundException

	public FileInputStream(File file) throws FileNotFoundException

	public FileInputStream(FileDescriptor fdObj)

	public final FileDescriptor getFD() throws IOException

	public FileChannel getChannel()

## java.io.FileReader ##

	public class FileReader extends InputStreamReader

	public FileReader(String fileName) throws FileNotFoundException

	public FileReader(File file) throws FileNotFoundException

	public FileReader(FileDescriptor fd)

## java.io.FileOutputStream ##

	public class FileOutputStream extends OutputStream

	public FileOutputStream(String name) throws FileNotFoundException

	public FileOutputStream(String name, boolean append) 
		throws FileNotFoundException

	public FileOutputStream(File file) throws FileNotFoundException

	public FileOutputStream(File file, boolean append)
        throws FileNotFoundException

	public FileOutputStream(FileDescriptor fdObj)

	public final FileDescriptor getFD()

	public FileChannel getChannel()

## java.io.FileWriter ##

	public class FileWriter extends OutputStreamWriter

	public FileWriter(String fileName) throws IOException

	public FileWriter(String fileName, boolean append) throws IOException

	public FileWriter(File file) throws IOException

	public FileWriter(File file, boolean append) throws IOException

	public FileWriter(FileDescriptor fd)

## java.io.PrintStream ##

	public class PrintStream extends FilterOutputStream implements Appendable, Closeable

	public PrintStream(OutputStream out)

	public PrintStream(OutputStream out, boolean autoFlush)

	public PrintStream(OutputStream out, boolean autoFlush, String encoding)
        throws UnsupportedEncodingException

	public PrintStream(String fileName) throws FileNotFoundException

	public PrintStream(String fileName, String csn)
        throws FileNotFoundException, UnsupportedEncodingException

	public PrintStream(File file) throws FileNotFoundException

	public PrintStream(File file, String csn)
        throws FileNotFoundException, UnsupportedEncodingException

	public void print(Object obj)

	public void println(Object x)
	public void println()  //newLine

	public PrintStream printf(String format, Object ... args)

	public PrintStream format(String format, Object ... args)

## java.io.InputStreamReader ##

	public class InputStreamReader extends Reader

	public InputStreamReader(InputStream in)

	public InputStreamReader(InputStream in, String charsetName)
        throws UnsupportedEncodingException

	public InputStreamReader(InputStream in, Charset cs)

	public String getEncoding()
	

## java.io.OutputStreamWriter ##

	public class OutputStreamWriter extends Writer

	public OutputStreamWriter(OutputStream out, String charsetName)
        throws UnsupportedEncodingException

	public OutputStreamWriter(OutputStream out)

	public OutputStreamWriter(OutputStream out, Charset cs)

	public String getEncoding()

## java.io.BufferedReader ##

	public class BufferedReader extends Reader

    private static int defaultCharBufferSize = 8192;
    private static int defaultExpectedLineLength = 80;

	public BufferedReader(Reader in)
	public BufferedReader(Reader in, int sz)

	public String readLine() throws IOException

	public Stream<String> lines()  //since 1.8

## java.io.BufferedInputStream ##

	public class BufferedInputStream extends FilterInputStream

	private static int DEFAULT_BUFFER_SIZE = 8192;

	public BufferedInputStream(InputStream in)
	public BufferedInputStream(InputStream in, int size)

## java.io.BufferedWriter ##

	private static int defaultCharBufferSize = 8192;

	public BufferedWriter(Writer out)
	public BufferedWriter(Writer out, int sz)

	public void newLine() throws IOException

## java.io.BufferedOutputStream ##
	
	public class BufferedOutputStream extends FilterOutputStream

	public BufferedOutputStream(OutputStream out)
	public BufferedOutputStream(OutputStream out, int size)

## java.io.RandomAccessFile ##

	public class RandomAccessFile implements DataOutput, DataInput, Closeable

	public RandomAccessFile(String name, String mode)
        throws FileNotFoundException

	public RandomAccessFile(File file, String mode)
        throws FileNotFoundException

	public final FileDescriptor getFD() throws IOException

	public final FileChannel getChannel()

	public int read() throws IOException

	public int read(byte b[], int off, int len) throws IOException

	public int read(byte b[]) throws IOException

	public int skipBytes(int n) throws IOException

	public void write(int b) throws IOException

	public void write(byte b[]) throws IOException

	public void write(byte b[], int off, int len) throws IOException

	public native long getFilePointer() throws IOException;

	public void seek(long pos) throws IOException

	public native long length() throws IOException;

	public native void setLength(long newLength) throws IOException;

	public void close() throws IOException

	public final String readLine() throws IOException

	