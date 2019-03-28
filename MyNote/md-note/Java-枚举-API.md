## java.lang.Enum<E> ##

	public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable

	private final String name;
	private final int ordinal;

	public final String name() {
        return name;
    }

	public final int ordinal() {
        return ordinal;
    }

	protected Enum(String name, int ordinal) {
        this.name = name;
        this.ordinal = ordinal;
    }
		//此构造器由编译器调用，在定义枚举类时，参数name即表示所列出的枚举值；参数ordinal
			表示所列出的枚举值在枚举类中的索引，即枚举值在枚举类中声明的位置。第一个枚举值的索引为0

	public String toString() {
        return name;
    }

	public static <T extends Enum<T>> T valueOf(Class<T> enumType, String name)
		//返回enumType枚举类中名为name的枚举值。

	public final int compareTo(E o)
		//用于比较调用此方法的枚举对象和参数枚举对象的ordinal索引大小。调用此方法的枚举对象和参数
			枚举对象必须是相同类型。
	