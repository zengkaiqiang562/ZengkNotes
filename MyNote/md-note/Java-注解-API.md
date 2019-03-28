## java.lang.annotation.Annotation ##

	boolean equals(Object obj);
	int hashCode();
	String toString();

	Class<? extends Annotation> annotationType();

## java.lang.reflect.AnnotatedElement ##

	public interface AnnotatedElement

	default boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)

	<T extends Annotation> T getAnnotation(Class<T> annotationClass);

	default <T extends Annotation> T[] getAnnotationsByType(Class<T> annotationClass)

	Annotation[] getAnnotations();

	default <T extends Annotation> T getDeclaredAnnotation(Class<T> annotationClass)

	default <T extends Annotation> T[] getDeclaredAnnotationsByType(
			Class<T> annotationClass)

	Annotation[] getDeclaredAnnotations();

## java.lang.annotation.Target ##

	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Target

	ElementType[] value();

## java.lang.annotation.ElementType ##

	public enum ElementType

    TYPE,  //Class, interface (including annotation type), or enum declaration

    FIELD,  //Field declaration (includes enum constants)

    METHOD,  //Method declaration

    PARAMETER,  //Formal parameter declaration

    CONSTRUCTOR,  //Constructor declaration

    LOCAL_VARIABLE,  //Local variable declaration

    ANNOTATION_TYPE,  //Annotation type declaration

    PACKAGE,  //Package declaration

    TYPE_PARAMETER,  //@since 1.8 , Type parameter declaration

    TYPE_USE  //@since 1.8 , Use of a type

## java.lang.annotation.Retention ##

	@Documented
	@Retention(RetentionPolicy.RUNTIME)
	@Target(ElementType.ANNOTATION_TYPE)
	public @interface Retention

	RetentionPolicy value();

## java.lang.annotation.RetentionPolicy ##

	public enum RetentionPolicy

    SOURCE,  //Annotations are to be discarded by the compiler.

    CLASS,  //Annotations are to be recorded in the class file by the compiler
				but need not be retained by the VM at run time.  This is the default
				behavior.

    RUNTIME  //Annotations are to be recorded in the class file by the compiler and
				retained by the VM at run time, so they may be read reflectively.

## javax.annotation.processing.AbstractProcessor ##

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

## javax.annotation.processing.RoundEnvironment ##

	public interface RoundEnvironment

	Set<? extends Element> getElementsAnnotatedWith(Class<? extends Annotation> a);
		//获取被注解a修饰的Element集合，此处返回的Element表示的程序元素是类

## javax.lang.model.element.Element ##
Element表示程序元素，可以是包、类、构造器、方法、成员变量等

	public interface Element extends javax.lang.model.AnnotatedConstruct

	Name getSimpleName(); //获取此Element表示的程序元素的名字，如当表示类时，返回的是类名

	<A extends Annotation> A getAnnotation(Class<A> annotationType);
		//获取修饰此程序元素的注解，
			参数annotationType指定要获取的注解类型

	ElementKind getKind();  //返回此Element所表示的程度元素的类型，
									如包、类、构造器、方法、成员变量等

	List<? extends Element> getEnclosedElements();
		//返回此程序元素范围内包含的程序元素，如类中包含有构造器，方法，成员变量

## javax.lang.model.element.ElementKind ##

	public enum ElementKind

    PACKAGE,

    --------------Declared types------------

    ENUM,  //An enum type

    CLASS,  //A class not described by a more specific kind (like ENUM)

    ANNOTATION_TYPE,  //An annotation type

    INTERFACE,  //An interface not described by a more specific kind (like ANNOTATION_TYPE)

    -------------Declared Variables--------------
    ENUM_CONSTANT,  //An enum constant

    FIELD,  //A field not described by a more specific kind (like ENUM_CONSTANT)

    PARAMETER,  //A parameter of a method or constructor

    LOCAL_VARIABLE, //A local variable

    EXCEPTION_PARAMETER,  //A parameter of an exception handler

    ------------Declared Executables--------------
    METHOD,  //A method

    CONSTRUCTOR,  //A constructor

    STATIC_INIT,  //A static initializer

    INSTANCE_INIT,  //An instance initializer

    /** A type parameter. */
    TYPE_PARAMETER,  //A type parameter
    
    OTHER,  //An implementation-reserved element. 
				This is not the element you are looking for.

    RESOURCE_VARIABLE;  //A resource variable