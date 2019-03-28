## Annotation概要
- 从JDK 5开始，Java增加了对元数据（MetaData）的支持，也就是注解Annotation；

- Annotation就是代码里的特殊标记，这些标记可以在编译、类加载、运行时被读取，并执行相应的处理（通过反射
实现）；

- Annotation提供了一种为程序元素（包、类、构造器、方法、成员变量等）设置元数据的方法，从某些方面来看，Annotation就像修饰符一样，可用于修饰包、类、构造器、方法、成员变量、参数、局部变量。

- 通过使用注解，开发人员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充信息，代码分析工具、开发工具
和部署工具可以通过这些补充信息进行验证或进行部署。

- 值得指出的是，如果希望让程序中的Annotation在运行时起作用，只有通过某种配套的工具对Annotation中的
信息进行访问和处理（通过反射实现）。访问和处理Annotation的工具统称为APT，即Annotation Processing 
Tool。可以将此APT注解处理工具理解成一套用于获取并处理Annotation注解的程序代码。
**没有使用APT注解处理工具处理过的Annotation，在程序中使用是不会起任何作用的。**


- 另外Annotation除了用来描述注解这一概念之外，它还表示一个接口。Java代码中，通过@interface定义的注解类型都默认实现了Annotation接口，通过反射获取的注解就是一个Annotation对象。

	java.lang.annotation.Annotation
		接口方法： Class<? extends Annotation> annotationType();

- 在Java代码中，对于可以接受注解的程序元素，需要实现AnnotatedElement接口。如Class(类)、Constructor(构造器)、Field(成员变量)、Method(成员方法)、Package(包)，它们都实现了AnnotatedElement接口

----
## 自定义Annotation
	//定义注解类型

	@元注解
	...
	@元注解
	public @interface 注解名称 {
		//定义成员变量
		成员变量类型 成员变量名() [default 默认值]
		成员变量类型 成员变量名() [default 默认值]
		...
	}

- 使用@interface关键字定义注解类型
- 定义注解类型时，还可以通过一个或多个元注解进行修饰
- 注解类型可以定义成员变量，也可以不定义成员变量
- 成员变量的类型只能是：8种基本数据类型、String、Class、注解类型、枚举类型、以及这些类型的一维数组类型
- 成员变量可以通过default关键字设置默认值，设置了默认值的成员变量，在使用注解时可以省略不赋值。
- 如果定义注解时，定义了一个名为value的成员变量，并且使用此注解时只需要为此value成员变量赋值（存在两种情况，一是只定义了成员变量value；而是除了value之外的其他成员变量在定义时都设置了默认值），则使用该注解时可以直接在该注解后的圆括号里指定成员变量value的值，无需使用“value=变量值”的形式。此时，若value表示的是一个数组类型，并且指定值时只需要指定一个元素值，则可以不用加花括号。

**1. 定义注解** 

	//定义无成员变量的注解类型
	public @interface Annotation0 {
	}

	//定义有成员变量的注解类型
	public @interface Annotation1 {
		String name();
		int age();  
		String[] children();
		Class[] clazzes();
	}

	//定义有成员变量的注解类型，并给部分成员变量设置了默认值
	public @interface Annotation2 {
		String name();
		int age();
		String city() default "shenzhen";
	}

	//定义存在一个value成员变量的注解。因为成员变量name设置了默认值，所有使用此注解时，可以只给value成员变量赋值
	public @interface Annotation3 {
		String[] value();
		String name() default "19";
	}

	//定义被多个元注解修饰的注解类型
	@Documented  //使用此元注解修饰的注解Annotation4将被javadoc工具提取成文档
	@Target({ElementType.FIELD,ElementType.METHOD}) //使用此元注解修饰Annotation4后，Annotation4注解只能用来修饰指定的程序元素：成员变量和方法。
	@Retention(RetentionPolicy.RUNTIME) //使用此元注解修饰的Annotation4，可以保留到运行时。
	public @interface Annotation4 {
		String name();
	}

**2. 使用注解**
通过“@ + 注解类型名 +赋值列表(可省略)”的方法使用注解：
	
	@Annotation0
	public void method(){
	}

	@Annotation1(name="zengk", age=26, children={"aa", "bb"}, clazzes={Object.class, TestAnnotation1.class})
	public void method(){
	}

	//没有对存在默认值的city成员变量赋值
	@Annotation2(name="zengk", age=26)
	public void method(){
	}

	//@Annotation3(value={"xx","yy"})
	@Annotation3({"xx","yyy"})
    public void method(){
	}

	//@Annotation3(value={"yy"}) 此方式也可以
	//@Annotation3(value="yy") 此方式也可以
	//@Annotation3({"yy"}) 此方式也可以
	@Annotation3("yy")
	public void method(){
	}

- 根据Annotation在定义时是否有成员变量，可以把Annotation分为两类：
	>标记Annotation： 没有定义成员变量的Annotation类型被称为标记。这种Annotation仅利用自身的存在与否来提供信息。如@Override
	元数据Annotation： 定义了成员变量的Annotation，因为他们可以接受更多的元数据，所以也被称为元数据Annotation。

## 用于修饰注解的元注解（Meta Annotation）
**1. @Retention**

	package java.lang.annotation;
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Retention {
	    RetentionPolicy value();
	}

- @Retention只能用于修饰Annotation，用于指定被修饰的Annotation可以保留多长时间。@Retention元注解只定义了一个RetentionPolicy枚举类型的value成员变量，value成员变量的值只有如下三个：

		RetentionPolicy.CLASS： 编译器把Annotation记录在class文件中。当运行java程序时，
			JVM不可获取Annotation信息。这是默认值。

		RetentionPolicy.RUNTIME： 编译器把Annotation记录在class文件中。当运行java程序时，
			JVM可以获取Annotation信息，程序可以通过反射获取该Annotation信息。

		RetentionPolicy.SOURCE： Annotation只保留在源代码中，编译器直接丢弃这种Annotation。

**如果需要通过反射获取某个注解的元数据，则必须使用@Retention(RetentionPolicy.RUNTIME)来修饰此注解类型。**

**2. @Target**

	package java.lang.annotation;
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Target {
	    ElementType[] value();
	}

- @Target只能用于修饰Annotation，用于指定被修饰的Annotation能用于修饰哪些程序元素。@Target元注解只定义了一个ElementType[]数组类型的value成员变量（其中ElementType是枚举类型），value成员变量的值可以是如下几个：

	    ElementType.TYPE： 指定该策略的Annotation只能修饰类，接口（包括注解类型），枚举定义。
		ElementType.FIELD： 指定该策略的Annotation只能修饰成员变量。
		ElementType.METHOD： 指定该策略的Annotation只能修饰方法定义。
		ElementType.PARAMETER： 指定该策略的Annotation只能修饰参数。
		ElementType.CONSTRUCTOR： 指定该策略的Annotation只能修饰构造方法。
		ElementType.LOCAL_VARIABLE： 指定该策略的Annotation只能修饰局部变量。
		ElementType.ANNOTATION_TYPE： 指定该策略的Annotation只能修饰Annotation。
		ElementType.PACKAGE： 指定该策略的Annotation只能修饰包定义。

**3. @Documented**

	package java.lang.annotation;
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Documented {
	}

- @Documented用于指定被该元注解修饰的Annotation类将被javadoc工具提取成文档，如果定义Annotation类时使用了@Documented修饰，则所有使用该Annotation修饰的程序元素的API文档中将会包含该Annotation说明

**4. @Inherited**

	package java.lang.annotation;
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Inherited {
	}

- @Inheritd用于指定被它修饰的Annotation将具有继承性。如果定义Annotation类时使用了@Inherited修饰，那么当使用该Annotation修饰了某个类，则这个类的子类也将自动被此Annotation修饰。

----
## Java中提供的基本Annotation介绍
**1. @Override**

	package java.lang;
	
	import java.lang.annotation.*;
	
	@Target(ElementType.METHOD)
	@Retention(RetentionPolicy.SOURCE)
	public @interface Override {
	}

- @Override用来修饰方法，强制类中的方法必须是重写了父类中的方法。当一个类中的某个方法使用@Override修饰后，则必须保证此类的父类或父接口中定义了一个被该方法重写的方法，否则会编译报错。使用@Override可以避免重写方法时的笔误。

**2. @Deprecated**

	package java.lang;
	
	import java.lang.annotation.*;
	import static java.lang.annotation.ElementType.*;
	
	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(value={CONSTRUCTOR, FIELD, LOCAL_VARIABLE, METHOD, PACKAGE, PARAMETER, TYPE})
	public @interface Deprecated {
	}

- @Deprecated用于表示某个程序元素已过时。当程序元素（如成员变量，方法等）被@Deprecated修饰后的，调用这些程序元素时，编译器将会给出警告。

**3. @SuppressWarnings**

	package java.lang;
	
	import java.lang.annotation.*;
	import static java.lang.annotation.ElementType.*;
	
	@Target({TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE})
	@Retention(RetentionPolicy.SOURCE)
	public @interface SuppressWarnings {
	    String[] value();
	}

- @SuppressWarnings修饰某个程序元素后，对于作用在此程序元素上的@SuppressWarning指定的编译器警告会被取消掉。如果某个程序元素被@SuppressWarnings修饰，那么该程序元素范围内的其他程序元素也会被此@SuppressWarnings修饰。例如当一个类被@SuppressWarnings修饰，此类中的某个方法又被另一个@SuppressWarnings修饰，那么对于这两个@SuppressWarnings指定的所有编译器警告，作用在此方法上时都会被取消。

----
## 提取注解信息
- 对于用@Retention(RetentionPolicy.RUNTIME)修饰的运行时注解，可以通过反射提取Annotation中的元数据。由于是运行时才处理注解信息，并且使用了大量的反射技术，所以效率会比较慢。
- 对于用@Retention(RetentionPolicy.CLASS)或@Retention(RetentionPolicy.SOURCE)修饰的注解，需要在编译时处理，此时不能使用反射技术，需通过javax.annotation.processing包下的Processor接口来实现一个注解处理器（Annotation Processor），一般采用继承AbstractProcessor的方式定义一个注解处理器。通过使用Annotation处理器来处理和提取Annotation信息。

### 通过反射技术提取运行时注解信息 ###

- 上文已经提到过，在Java中，通过Annotation对象表示一个注解。所以只有得到注解的Annotation对象才能提取此注解中的元数据。
那么怎么得到Annotation对象呢？上文也有提到，对于可以接受注解的程序元素，需要实现AnnotatedElement接口，而其中的AnnotationElement接口就提供了获取Annotation对象的方法。所以只需要得到AnnotatedElement的实现类对象，就能获取到Annotation对象，从而提取Annotation中的元数据。而AnnotatedElement的实现类，在上文中也有提到，就是Class、Method、Field、Constructor等这些程序元素在Java中对应的类。于是，我们可以通过反射获取到Class、Constructor、Method、Field这些类的对象。

- 通过反射提取Annotation中的元数据的步骤总结如下：
	1. 通过反射得到Class、Constructor、Method、Field这些实现了AnnotationElement接口的类的对象。
	2. 通过AnnotationElement实现类对象调AnnotationElement提供的API，获取注解对应的Annotation对象。
	3. 通过Annotation对象获取到注解的元数据。


### 通过AbstractProcessor构建注解处理器，提取编译时注解信息 ###
**step1. 自定义AbstractProcessor子类，处理注解**
AbstractProcessor继承自Processor，自定义AbstractProcessor是重写process方法处理注解信息

	public abstract class AbstractProcessor implements Processor

	public Set<String> getSupportedAnnotationTypes()
		//重写此方法。返回此注解处理器APT 处理的注解类型，
			返回Set集合包含要处理的注解类型，String表示注解类名（最好使用全路径名）
		//Java1.7以后也可以不重写此方法，通过注解@SupportedAnnotationTypes指定处理的注解类型，
			@SupportedAnnotationTypes的目标程序元素是类，即在自定义的AbstractProcessor子类上使用

	public SourceVersion getSupportedSourceVersion()
		//重写此方法，返回处理注解时采用的Java版本
		//Java1.7以后也可以不重写此方法，通过注解@SupportedSourceVersion指定Java版本
			@SupportedAnnotationTypes也是作用在自定义的AbstractProcessor子类上

	public synchronized void init(ProcessingEnvironment processingEnv)
		//通常不需要重写此方法

	public abstract boolean process(Set<? extends TypeElement> annotations,
                                    RoundEnvironment roundEnv);
		//重写此方法，处理注解。

//TODO


**AnnotationElement接口提供的API（JDK 7）**
`boolean isAnnotationPresent(Class<? extends Annotation> annotationClass);` ： 判断该程序元素上是否存在指定注解类型的注解，存在返回true，否则返回false。
`<T extends Annotation> T getAnnotation(Class<T> annotationClass);` ： 返回此程序元素上存在的、指定注解类型的Annotation对象。如果该注解类型的注解不存在，则返回null。
`Annotation[] getAnnotations();` ： 返回该程序元素上存在的所有注解。
`Annotation[] getDeclaredAnnotations();` ： 返回直接修饰该程序元素的所有注解。

## 举例：
### 一、通过反射处理运行时Annotation，实现Android中Button按键的点击事件绑定
**1. 定义注解类型**

	@Target(ElementType.FIELD)
	@Retention(RetentionPolicy.RUNTIME)
	public @interface ButtonAnnotation {
	    int resId();
	    Class<? extends View.OnClickListener> listenClazz();
	}

**2. 定义注解处理工具**

	public class ButtonAnnotationProcessTools {
	    public static void process(Object obj){

	        //获取使用了注解的程序元素所在的类的class对象
	        Class clazz = obj.getClass();

	        //因为@ButtonAnnotation只能修饰成员变量，所以只需变量此类中的成员变量，找到被
				@ButtonAnnotation修饰的成员变量
	        Field[] declaredFields = clazz.getDeclaredFields();

	        for(Field field : declaredFields){

	            //因为后面需要给@ButtonAnnotation修饰的成员变量赋值，所以对于私有的成员变量，
					需获取访问权限
	            field.setAccessible(true);

	            //判断成员变量是否被@ButtonAnnotation修饰
	            boolean annotationPresent = field.isAnnotationPresent(
						ButtonAnnotation.class);

	            if(annotationPresent){

	                //获取修饰此成员变量的ButtonAnnotation注解对象
	                ButtonAnnotation buttonAnnotation = field.getAnnotation(
							ButtonAnnotation.class);

	                try {

	                    Class fieldType = field.getType();

	                    //对Button类型的成员变量，通过注解@ButtonAnnotation中的元数据对其进行赋值
							和设置点击事件监听
	                    if (fieldType.equals(Button.class) && obj instanceof MainActivity){

	                        Activity activity = (Activity) obj;

	                        //通过@ButtonAnnotation中的资源ID得到view对象
	                        View view = activity.findViewById(buttonAnnotation.resId());
	
	                        //通过@ButtonAnnotation中的监听器Class对象，利用反射得到
								View.OnClickListener对象
	                        Class<? extends View.OnClickListener> listenClazz = 
								buttonAnnotation.listenClazz();

	                        View.OnClickListener onClickListener =  listenClazz
									.newInstance();

	                        view.setOnClickListener(onClickListener);
	
	                        //给@ButtonAnnotation修饰的成员变量赋值
	                        field.set(obj, view);
	
	                    }
	                } catch (IllegalAccessException e) {
	                    e.printStackTrace();
	                } catch (InstantiationException e) {
	                    e.printStackTrace();
	                } catch (InvocationTargetException e) {
	                    e.printStackTrace();
	                } catch (NoSuchMethodException e) {
	                    e.printStackTrace();
	                }
	            }
	        }
	    }
	}

**3. 使用注解对Button类型的成员变量进行赋值并绑定点击事件监听器**

	public class MainActivity extends AppCompatActivity{
	
	    @ButtonAnnotation(resId = R.id.btn, listenClazz = ButtonOnClickListener.class)
	    private Button mBtn;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
			//使用注解处理工具对@ButtonAnnotation进行处理
	        ButtonAnnotationProcessTools.process(this);
	    }
	
	    class ButtonOnClickListener implements View.OnClickListener{
	        @Override
	        public void onClick(View v) {
	            Log.d(TAG, "onclick");
	        }
		}
	}

	