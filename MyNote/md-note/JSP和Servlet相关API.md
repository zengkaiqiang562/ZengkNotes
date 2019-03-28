javax.servlet.Servlet

	public String getServletInfo();

	public void service(ServletRequest req, ServletResponse res)
		throws ServletException, IOException;

	public ServletConfig getServletConfig();

	public void init(ServletConfig config) throws ServletException;
	
	public void destroy();

javax.servlet.GenericServlet
	
	public abstract class GenericServlet implements Servlet, ServletConfig,
        java.io.Serializable

	@Override
	public ServletConfig getServletConfig()

	@Override
	public ServletContext getServletContext()

	@Override
	public String getServletName()


javax.servlet.http.HttpServlet

	public abstract class HttpServlet extends GenericServlet

	protected void service(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException

	@Override
	public void service(ServletRequest req, ServletResponse res)
        throws ServletException, IOException

org.apache.jasper.runtime.HttpJspBase

	public abstract class HttpJspBase extends HttpServlet implements HttpJspPage

	@Override
	public String getServletInfo()

org.apache.jasper.runtime.JspRuntimeLibrary

	public static void include(ServletRequest request, ServletResponse response,
                               String relativePath, JspWriter out, boolean flush)
        throws IOException, ServletException


javax.servlet.jsp.PageContext

	public abstract class PageContext extends JspContext

	public static final int PAGE_SCOPE = 1;
	public static final int REQUEST_SCOPE = 2;
	public static final int SESSION_SCOPE = 3;
	public static final int APPLICATION_SCOPE = 4;

	public abstract ServletContext getServletContext();

	public abstract ServletConfig getServletConfig();

	public abstract ServletRequest getRequest();

	public abstract ServletResponse getResponse();

	public abstract HttpSession getSession();

	public abstract Object getPage();

	public abstract Exception getException();

	public abstract void forward(String relativeUrlPath) throws ServletException, IOException;

	public abstract void include(String relativeUrlPath)  throws ServletException, IOException;

	public abstract void include(String relativeUrlPath, boolean flush) throws ServletException, IOException;

javax.servlet.jsp.JspContext

	public abstract JspWriter getOut();

	public abstract void setAttribute(String name, Object value);
	public abstract void setAttribute(String name, Object value, int scope);
	public abstract Object getAttribute(String name);
	public abstract Object getAttribute(String name, int scope);
	public abstract void removeAttribute(String name);
	public abstract void removeAttribute(String name, int scope);

	public abstract Object findAttribute(String name);

	public abstract int getAttributesScope(String name);

	public abstract Enumeration<String> getAttributeNamesInScope(int scope);	

javax.servlet.ServletContext

	public void setAttribute(String name, Object object);
	public Object getAttribute(String name);
	public Enumeration<String> getAttributeNames();
	public void removeAttribute(String name);

	public boolean setInitParameter(String name, String value);
	public String getInitParameter(String name);
	public Enumeration<String> getInitParameterNames();

	public RequestDispatcher getRequestDispatcher(String path);

	public String getRealPath(String path);


javax.servlet.ServletConfig

	public String getInitParameter(String name);
	public Enumeration<String> getInitParameterNames();

	public ServletContext getServletContext();
	
	public String getServletName();


javax.servlet.jsp.JspWriter
	
	public abstract class JspWriter extends java.io.Writer

	public abstract void print(Object obj) throws IOException;

	public abstract void println(Object x) throws IOException;


javax.servlet.ServletRequest
	
	public ServletContext getServletContext();

	public RequestDispatcher getRequestDispatcher(String path);

	public void setAttribute(String name, Object o);
	public Object getAttribute(String name);
	public Enumeration<String> getAttributeNames();
	public void removeAttribute(String name);

	public void setCharacterEncoding(String env) throws java.io.UnsupportedEncodingException;

	public String getCharacterEncoding();

	public String getContentType();

	public int getContentLength();

	public ServletInputStream getInputStream() throws IOException;

	public BufferedReader getReader() throws IOException;

	public String getProtocol();

	public String getRemoteAddr();

	public String getRemoteHost();

	public int getRemotePort();

	public String getParameter(String name);
	public String[] getParameterValues(String name);
	public Enumeration<String> getParameterNames();
	public Map<String, String[]> getParameterMap();

javax.servlet.http.HttpServletRequest

	public interface HttpServletRequest extends ServletRequest

	public String getMethod();

	public Cookie[] getCookies();

	public String getHeader(String name);
	public Enumeration<String> getHeaders(String name);
	public Enumeration<String> getHeaderNames();

	public String getPathInfo();

	public String getRequestedSessionId();

	public String getRequestURI();

	public StringBuffer getRequestURL();

	public String getServletPath();

	public HttpSession getSession(boolean create);

	public HttpSession getSession();

	public Collection<Part> getParts() throws IOException, ServletException;

	public Part getPart(String name) throws IOException, ServletException;

	public String getQueryString();  查询字符串就是url地址后附加的键值对请求参数

javax.servlet.RequestDispatcher

	public void forward(ServletRequest request, ServletResponse response)
            throws ServletException, IOException;

	public void include(ServletRequest request, ServletResponse response)
            throws ServletException, IOException;

javax.servlet.ServletResponse

	public void setCharacterEncoding(String charset);
	public String getCharacterEncoding();

	public String getContentType();

	public ServletOutputStream getOutputStream() throws IOException;
	public PrintWriter getWriter() throws IOException;

	public void setContentLength(int len);
	public void setContentType(String type);

javax.servlet.http.HttpServletResponse

	public interface HttpServletResponse extends ServletResponse

	public void addCookie(Cookie cookie);

	public boolean containsHeader(String name);
	public void setHeader(String name, String value);
	public void addHeader(String name, String value);

	public String getHeader(String name);
	public Collection<String> getHeaders(String name);
	public Collection<String> getHeaderNames();

	public void setStatus(int sc);
	public int getStatus();

	public void sendRedirect(String location) throws IOException;


javax.servlet.http.HttpSession

	public ServletContext getServletContext();

	public long getCreationTime();

	public long getLastAccessedTime();

	public String getId();

	public void setAttribute(String name, Object value);
	public Object getAttribute(String name);
	public Enumeration<String> getAttributeNames();
	public void removeAttribute(String name);

	public void invalidate();

	public boolean isNew();

javax.servlet.http.Part

	public InputStream getInputStream() throws IOException;
	public String getContentType();
	public String getName();
	public long getSize();
	public String getHeader(String name);
	public Collection<String> getHeaders(String name);
	public Collection<String> getHeaderNames();

	public void write(String fileName) throws IOException;



	

	