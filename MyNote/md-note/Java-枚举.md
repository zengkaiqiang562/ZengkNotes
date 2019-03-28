## 枚举类概述 ##
在某些情况下，一个类的对象是有限且固定的（如季节类，只有4个对象），这种实例有限且固定的类，在Java中被
称为枚举类。

**枚举的早期表现形式**
在早期代码中，可能会直接使用简单的静态常量来表示枚举：
	
	public static final int SEASON_SPRING = 1;
	public static final int SEASON_SUMMER = 2;
	public static final int SEASON_FALL = 3;
	public static final int SEASON_WINTER = 4;

通过静态常量表示枚举存在如下几个问题：
- 类型不安全。上例中，每个季节通过int整数表示，所以完全可以把一个季节当成int整数使用，如进行算数运算。
- 没有命名空间。当需要使用季节时，必须在SPRING前加上SEASON_前缀。
- 打印输出的意义不明确。当打印季节时，实际输出的是这个季节对应的整数值，根据整数值难以看出它表示春天。

**使用 enum 关键字定义枚举类**
Java 5新增 enum 关键字用来定义枚举类。enum关键字跟class、interface关键字的地位相同。

枚举类可以有自己的成员变量、成员方法；可以实现一个或多个接口；可以定义自己的构造器。

一个Java源文件中最多只能定义一个public访问权限的枚举类，且该Java源文件名也必须和public修饰的枚举类
类名相同。

**枚举类的特性**
枚举类的特性(与普通类的区别)：
- 枚举类可以实现一个或多个接口，使用enum定义的枚举类默认继承java.lang.Enum类，而并非默认继承
java.lang.Object，因此枚举类不能显示继承其他类。java.lang.Enum类实现了Serializable和Comparable
这两个接口。

- 使用enum定义的非抽象的枚举类，默认使用final修饰，因此不包含抽象方法的非抽象的枚举类不能派生子类。
使用enum定义的包含抽象方法的枚举类，则默认使用abstract修饰。如实现了一个包含有抽象方法的接口的枚举类，
默认使用abstract修饰。再如直接包含有一个抽象方法的枚举类，也默认使用abstract修饰。

- 枚举类中定义抽象方法时，不能显示添加abstract关键字将枚举类定义成抽象类（系统会自动添加）。因为枚举
类需要显示创建枚举值，而不是作为父类。所以，定义每个枚举值时必须为抽象方法提供实现，否则编译错误。

- 枚举类的构造器只能使用private访问控制符。如果省略了构造器的访问控制符，则默认使用private修饰。

- 枚举类的所有实例必须在枚举类的第一行显示列出，否则这个枚举类永远都不能产生实例。列出这些实例时，系统
会自动添加 public static final 修饰，无须程序员显示添加。

- 枚举类的实例只能是枚举值，不能通过new来创建枚举类对象


**java.lang.Enum类**
上面已说明所有枚举类都继承自Enum类，所以，所有枚举类都能使用Enum类中提供的如下方法：

	public final String name() {
        return name;
    }
		//在定义枚举类时，name即表示所列出的枚举值

	public final int ordinal() {
        return ordinal;
    }
		//ordinal表示所列出的枚举值在枚举类中的索引，即枚举值在枚举类中声明的位置。
			第一个枚举值的索引为0

	public String toString() {
        return name;
    }

	public static <T extends Enum<T>> T valueOf(Class<T> enumType, String name)
		//返回enumType枚举类中名为name的枚举值。

	public final int compareTo(E o)
		//用于比较调用此方法的枚举对象和参数枚举对象的ordinal索引大小。调用此方法的枚举对象和参数
			枚举对象必须是相同类型。

**定义枚举类**
	
	public enum SeasonEnum {
		SPRING,SUMMER,FALL,WINTER; 
			//列出枚举值时使用的是无参构造器，所以无须传入参数，甚至也无须使用括号
			等同于 public static final SeasonEnum SPRING = new SeasonEnum();
				  public static final SeasonEnum SUMMER = new SeasonEnum();
				  public static final SeasonEnum FALL = new SeasonEnum();
				  public static final SeasonEnum WINTER = new SeasonEnum();
	}

如上，定义枚举类时显示列出所有的枚举值（枚举实例），枚举值之间用逗号","隔开。枚举值列举结束后用分号";"
结束。所列出的枚举值即代表了该枚举类的所有可能的实例。

**枚举类的构造器、成员方法和成员变量**
枚举类也是一个类，同样可以定义成员变量、成员方法和构造器。

枚举类通常应该被设计为不可变类，也就是说，它的成员变量值不应该允许改变。因此建议将枚举类的成员变量都
使用private final修饰，并在构造器中为成员变量指定初始值。

	public enum Gender {

		MALE("男"),FEMALE("女");  
			//等同于 public static final Gender MALE = new Gender("男");
				    public static final Gender MALE = new Gender("女");

		private final String name;

		private Gender(String name) {
			this.name = name;
		}

		public String getName() {	
			return this.name;
		}
	}

如上， 在枚举类中列出枚举值时，实际上是调用了构造器创建枚举类对象，只是这里无须使用new关键字，也无须
显示调用构造器。
之前定义的SeasonEnum枚举类中，列出枚举值时使用的是无参构造器，所以无须传入参数，甚至也无须使用括号。
而在Gender枚举类中列出枚举值时，使用的是`private Gender(String name)`构造器。

**实现接口的枚举类**
枚举类不能显示继承其他类，但可以实现接口。实现接口的枚举类，也需要实现接口中的方法。

	public interface GenderDesc {
		void info();
	}

	public enum Gender implements GenderDesc {
		MALE("男"),FEMALE("女");  

		private final String name;

		private Gender(String name) {
			this.name = name;
		}

		public String getName() {	
			return this.name;
		}

		@Override
		public void info() {...}
	}

如上，在枚举类中实现接口方法时，每个枚举值在调用该方法时都有相同的行为方式。如果要为每个枚举值在调用
该方法时呈现出不同的行为方法，则不应该在枚举类中实现接口方法，而是在声明枚举值时分别实现接口方法：

	public interface GenderDesc {
		void info();
	}

	public enum Gender implements GenderDesc {
		MALE("男") {
			public void info(){...}
		},

		FEMALE("女") {
			public void info(){...}
		};  

			//类似于 public static final Gender MALE = new Gender("女"){
						public void info(){...}
					}；

		private final String name;

		private Gender(String name) {
			this.name = name;
		}

		public String getName() {	
			return this.name;
		}
	}

如上声明枚举值时，并不是直接创建Gender枚举类的实例，而是相当于创建Gender的匿名子类的实例（因为Gender
枚举类没有实现接口方法info()，所以Gender是一个抽象的枚举类）。

编译上面程序，可以看到生成Gender.class、Gender$1.class和Gender$2.class三个文件。这三个文件可以
证明枚举值 MALE 和 FEMALE 实际上是 Gender 的匿名子类的实例，而不是Gender类的实例。