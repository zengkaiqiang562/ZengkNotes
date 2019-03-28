## com.android.okhttp.HttpHandler ##

	public class HttpHandler extends URLStreamHandler

	@Override
	protected URLConnection openConnection(URL url) throws IOException {
		return newOkUrlFactory(null /* proxy */).open(url);
	}

	protected OkUrlFactory newOkUrlFactory(Proxy proxy) {
		OkUrlFactory okUrlFactory = createHttpOkUrlFactory(proxy);

		// For HttpURLConnections created through java.net.URL Android uses a
		// connection pool that
		// is aware when the default network changes so that pooled connections
		// are not re-used when
		// the default network changes.

		okUrlFactory.client().setConnectionPool(configAwareConnectionPool.get());
		return okUrlFactory;
	}

## com.squareup.okhttp.internal.huc.HttpURLConnectionImpl ##

	public class HttpURLConnectionImpl extends HttpURLConnection

	@Override
	public final void connect() throws IOException {
		initHttpEngine();
		boolean success;
		do {
			success = execute(false);
		} while (!success);
	}

	-------------------
	@Override
	public final OutputStream getOutputStream() throws IOException {
		connect();

		BufferedSink sink = httpEngine.getBufferedRequestBody();
		if (sink == null) {
			throw new ProtocolException("method does not support a request body: " + method);
		} else if (httpEngine.hasResponse()) {
			throw new ProtocolException("cannot write request body 
				after response has been read");
		}

		return sink.outputStream();
	}

	@Override
	public final InputStream getInputStream() throws IOException {
		if (!doInput) {
			throw new ProtocolException("This protocol does not support input");
		}

		HttpEngine response = getResponse();

		// if the requested file does not exist, throw an exception formerly the
		// Error page from the server was returned if the requested file was
		// text/html this has changed to return FileNotFoundException for all
		// file types
		if (getResponseCode() >= HTTP_BAD_REQUEST) { //HTTP_BAD_REQUEST = 400
			throw new FileNotFoundException(url.toString());
		}

		return response.getResponse().body().byteStream();
	}

	private HttpEngine getResponse() throws IOException {

		initHttpEngine();

		if (httpEngine.hasResponse()) {
			return httpEngine;
		}

		while (true) {

			if (!execute(true)) {
				continue;
			}

			...
		}
	}
	--------------------

	private Headers.Builder requestHeaders = new Headers.Builder();

	@Override
	public void setIfModifiedSince(long newValue) {
		super.setIfModifiedSince(newValue);
		if (ifModifiedSince != 0) {

			requestHeaders.set("If-Modified-Since", 
								HttpDate.format(new Date(ifModifiedSince)));

		} else {
			requestHeaders.removeAll("If-Modified-Since");
		}
	}

	@Override
	public final void setRequestProperty(String field, String newValue) {
		if (connected) {
			throw new IllegalStateException("Cannot set request property after connection is made");
		}
		if (field == null) {
			throw new NullPointerException("field == null");
		}
		if (newValue == null) {

			// Silently ignore null header values for backwards compatibility
			// with older
			// android versions as well as with other URLConnection
			// implementations.
			//
			// Some implementations send a malformed HTTP header when faced with
			// such requests, we respect the spec and ignore the header.

			Platform.get().logW("Ignoring header " + field 
					+ " because its value was null.");
			return;
		}

		// TODO: Deprecate use of X-Android-Transports header?
		if ("X-Android-Transports".equals(field) || "X-Android-Protocols".equals(field)) {
			setProtocols(newValue, false /* append */);
		} else {
			requestHeaders.set(field, newValue);
		}
	}

	@Override
	public final void addRequestProperty(String field, String value) {
		if (connected) {
			throw new IllegalStateException("Cannot add request property after connection is made");
		}
		if (field == null) {
			throw new NullPointerException("field == null");
		}
		if (value == null) {
			// Silently ignore null header values for backwards compatibility
			// with older
			// android versions as well as with other URLConnection
			// implementations.
			//
			// Some implementations send a malformed HTTP header when faced with
			// such requests, we respect the spec and ignore the header.
			Platform.get().logW("Ignoring header " + field 
				+ " because its value was null.");
			return;
		}

		// TODO: Deprecate use of X-Android-Transports header?
		if ("X-Android-Transports".equals(field) || "X-Android-Protocols".equals(field)) {
			setProtocols(value, true /* append */);
		} else {
			requestHeaders.add(field, value);
		}
	}

	private HttpEngine newHttpEngine(String method, StreamAllocation streamAllocation, 
			RetryableSink requestBody, Response priorResponse) 
				throws MalformedURLException, UnknownHostException {

		// OkHttp's Call API requires a placeholder body; 
		//		the real body will be streamed separately.

		RequestBody placeholderBody = HttpMethod.requiresRequestBody(method) ? 
											EMPTY_REQUEST_BODY : null;

		URL url = getURL();

		HttpUrl httpUrl = Internal.instance.getHttpUrlChecked(url.toString());

		Request.Builder builder = new Request.Builder().url(httpUrl)
										.method(method, placeholderBody);

		Headers headers = requestHeaders.build();

		for (int i = 0, size = headers.size(); i < size; i++) {
			builder.addHeader(headers.name(i), headers.value(i));
		}

		boolean bufferRequestBody = false;

		if (HttpMethod.permitsRequestBody(method)) {

			// Specify how the request body is terminated.
			if (fixedContentLength != -1) {

				builder.header("Content-Length", Long.toString(fixedContentLength));

			} else if (chunkLength > 0) {

				builder.header("Transfer-Encoding", "chunked");

			} else {
				bufferRequestBody = true;
			}

			// Add a content type for the request body, if one isn't already present.

			if (headers.get("Content-Type") == null) {
				builder.header("Content-Type", "application/x-www-form-urlencoded");
			}
		}

		if (headers.get("User-Agent") == null) {
			builder.header("User-Agent", defaultUserAgent());
		}

		Request request = builder.build();

		// If we're currently not using caches, make sure the engine's client
		// doesn't have one.

		OkHttpClient engineClient = client;

		if (Internal.instance.internalCache(engineClient) != null && !getUseCaches()) {
			engineClient = client.clone().setCache(null);
		}

		return new HttpEngine(engineClient, request, bufferRequestBody, 
						true, false, streamAllocation, requestBody, priorResponse);
	}