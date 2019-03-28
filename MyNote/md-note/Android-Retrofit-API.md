## retrofit2.http.GET ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface GET

	String value() default "";

## retrofit2.http.POST ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface POST

	String value() default "";

## retrofit2.http.HTTP ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface HTTP

	String method();
	String path() default "";
	boolean hasBody() default false;

## retrofit2.http.FormUrlEncoded ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface FormUrlEncoded

## retrofit2.http.Multipart ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface Multipart

## retrofit2.http.Streaming ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface Streaming

## retrofit2.http.Field ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface Field

	String value();
	boolean encoded() default false;

## retrofit2.http.FieldMap ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface FieldMap

	boolean encoded() default false;

## retrofit2.http.Part ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface Part

	String value() default "";
	String encoding() default "binary";

## retrofit2.http.Header ##

	@Documented
	@Retention(RUNTIME)
	@Target(PARAMETER)
	public @interface Header

	String value();

## retrofit2.http.Headers ##

	@Documented
	@Target(METHOD)
	@Retention(RUNTIME)
	public @interface Headers

	String[] value();

## retrofit2.http.Body ##
	
	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface Body

## retrofit2.http.Part ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface Part

	String value() default "";
	String encoding() default "binary";

## retrofit2.http.PartMap ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface PartMap

	String encoding() default "binary";

## retrofit2.http.Path ##

	@Documented
	@Retention(RUNTIME)
	@Target(PARAMETER)
	public @interface Path

	String value();
	boolean encoded() default false;

## retrofit2.http.Query ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface Query

	String value();
	boolean encoded() default false;

## retrofit2.http.QueryMap ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface QueryMap

	boolean encoded() default false;

## retrofit2.http.Url ##

	@Documented
	@Target(PARAMETER)
	@Retention(RUNTIME)
	public @interface Url

## retrofit2.Retrofit ##

	public final class Retrofit

	public <T> T create(final Class<T> service) 
		//参数service表示网络请求接口类的Class对象，T表示网络请求接口类，
		//此方法返回一个网络请求接口类的实例

### retrofit2.Retrofit.Builder ###

	public static final class Builder

	public Builder()

	public Builder baseUrl(String baseUrl)

	public Builder addConverterFactory(Converter.Factory factory)

	public Builder addCallAdapterFactory(CallAdapter.Factory factory)

	public Retrofit build()

## retrofit2.Call<T> ##

	public interface Call<T> extends Cloneable

	Response<T> execute() throws IOException;

	void enqueue(Callback<T> callback);

	void cancel();

	boolean isCanceled();

	Request request(); //The original HTTP request （okhttp3.Request）

## retrofit2.Callback<T> ##

	public interface Callback<T>

	void onResponse(Call<T> call, Response<T> response);

	void onFailure(Call<T> call, Throwable t);

## retrofit2.Response<T> ##

	public final class Response<T>

	public int code() //HTTP status code

	public Headers headers() //HTTP headers

	public boolean isSuccessful()  //Returns true if code() is in the range [200..300)

	public @Nullable T body()

	public okhttp3.Response raw()  //The raw response from the HTTP client