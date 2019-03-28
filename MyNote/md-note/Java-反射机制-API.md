## java.lang.Class<T> ##

	public final class Class<T> implements java.io.Serializable, GenericDeclaration,
                              Type, AnnotatedElement

	public static Class<?> forName(String className) throws ClassNotFoundException

	public native T newInstance() throws InstantiationException, IllegalAccessException;
	
	---------------
	public Constructor<T> getConstructor(Class<?>... parameterTypes)
        throws NoSuchMethodException, SecurityException {

        return getConstructor0(parameterTypes, Member.PUBLIC);
    }

	public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)
        throws NoSuchMethodException, SecurityException {

        return getConstructor0(parameterTypes, Member.DECLARED);
    }

	public Constructor<?>[] getConstructors() throws SecurityException {
        return getDeclaredConstructorsInternal(true); // true means publicOnly
    }

	public Constructor<?>[] getDeclaredConstructors() throws SecurityException {
        return getDeclaredConstructorsInternal(false);
    }
	------------------

	public Field getField(String name) throws NoSuchFieldException {
        if (name == null) {
            throw new NullPointerException("name == null");
        }
        Field result = getPublicFieldRecursive(name);
        if (result == null) {
            throw new NoSuchFieldException(name);
        }
        return result;
    }

	public native Field getDeclaredField(String name) throws NoSuchFieldException;

	public Field[] getFields() throws SecurityException {
        List<Field> fields = new ArrayList<Field>();
        getPublicFieldsRecursive(fields);
        return fields.toArray(new Field[fields.size()]);
    }

	public native Field[] getDeclaredFields();
	-------------------

	public Method getMethod(String name, Class<?>... parameterTypes)
        throws NoSuchMethodException, SecurityException {
        return getMethod(name, parameterTypes, true); //true means recursivePublicMethods
    }

	public Method getDeclaredMethod(String name, Class<?>... parameterTypes)
        throws NoSuchMethodException, SecurityException {
        return getMethod(name, parameterTypes, false);
    }

	public Method[] getMethods() throws SecurityException {
        List<Method> methods = new ArrayList<Method>();
        getPublicMethodsInternal(methods);

         //Remove duplicate methods defined by superclasses and
         //interfaces, preferring to keep methods declared by derived
         //types.
        CollectionUtils.removeDuplicates(methods, Method.ORDER_BY_SIGNATURE);
        return methods.toArray(new Method[methods.size()]);
    }

	public Method[] getDeclaredMethods() throws SecurityException {
        Method[] result = getDeclaredMethodsUnchecked(false); //false means not publicOnly
        for (Method m : result) {
            // Throw NoClassDefFoundError if types cannot be resolved.
            m.getReturnType();
            m.getParameterTypes();
        }
        return result;
    }

## java.lang.reflect.AccessibleObject ##

	public class AccessibleObject implements AnnotatedElement

	public void setAccessible(boolean flag) throws SecurityException {
        setAccessible0(this, flag);
    }
		//flag为true，表示取消访问权限的检查，于是就可以访问私有成员

	public static void setAccessible(AccessibleObject[] array, boolean flag)
        throws SecurityException {
        for (int i = 0; i < array.length; i++) {
            setAccessible0(array[i], flag);
        }
    }

## java.lang.reflect.Executable ##

	public abstract class Executable extends AccessibleObject
			implements Member, GenericDeclaration

## java.lang.reflect.Member ##

    public static final int PUBLIC = 0;
		//Identifies the set of all public members of a class or interface,
	 		including inherited members.

    public static final int DECLARED = 1;
		//Identifies the set of declared members of a class or interface.
			Inherited members are not included.

	public Class<?> getDeclaringClass();

	public String getName();

	public int getModifiers();

## java.lang.reflect.Constructor<T> ##

	public final class Constructor<T> extends Executable

	public T newInstance(Object ... initargs) throws InstantiationException, 		IllegalAccessException, IllegalArgumentException, InvocationTargetException

## java.lang.reflect.Field ##

	public final class Field extends AccessibleObject implements Member

	public Class<?> getType() //返回成员变量的类型

	public native void set(Object obj, Object value)
			throws IllegalArgumentException, IllegalAccessException;
		//给obj对象的field字段表示的成员变量赋值value

	public native Object get(Object obj)
        	throws IllegalArgumentException, IllegalAccessException;


## java.lang.reflect.Method ##

	public final class Method extends Executable

	public native Object invoke(Object obj, Object... args) throws IllegalAccessException,
			IllegalArgumentException, InvocationTargetException;
		//调用obj对象的method方法，args为method方法的参数，method方法没有参数则为null

## java.lang.reflect.Modifier ##

	public class Modifier

	public static final int PUBLIC           = 0x00000001;
	public static final int PRIVATE          = 0x00000002;
    public static final int PROTECTED        = 0x00000004;
    public static final int STATIC           = 0x00000008;
    public static final int FINAL            = 0x00000010;
    public static final int SYNCHRONIZED     = 0x00000020;
    public static final int VOLATILE         = 0x00000040;
    public static final int TRANSIENT        = 0x00000080;
    public static final int NATIVE           = 0x00000100;
    public static final int INTERFACE        = 0x00000200;
    public static final int ABSTRACT         = 0x00000400;
    public static final int STRICT           = 0x00000800;

	//mod a set of modifiers. see Member#getModifiers()
	public static boolean isPublic(int mod) { 
        return (mod & PUBLIC) != 0;
    }
    
    public static boolean isPrivate(int mod)
    
    public static boolean isProtected(int mod)

    public static boolean isStatic(int mod)

    public static boolean isFinal(int mod)

    public static boolean isSynchronized(int mod)

    public static boolean isVolatile(int mod)

    public static boolean isTransient(int mod)

    public static boolean isNative(int mod) {
        return (mod & NATIVE) != 0;
    }

    public static boolean isInterface(int mod)

    public static boolean isAbstract(int mod)

	public static boolean isStrict(int mod)

	public static boolean isConstructor(int modifiers) {
        return ((modifiers & Modifier.CONSTRUCTOR) != 0);
    }

	public static String toString(int mod) 