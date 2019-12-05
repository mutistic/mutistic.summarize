# 一、Java基础
## 1 包装类型
基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成

## 2 缓存池
#### 2.1、new Integer(123) 与 Integer.valueOf(123) 的区别在于：
new Integer(123) 每次都会新建一个对象；
Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。
```Java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y); // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k); // true
```

#### 2.2 valueOf()的实现细节：
valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。
```Java
public static Integer valueOf(int i) {
  if (i >= IntegerCache.low && i <= IntegerCache.high)
    return IntegerCache.cache[i + (-IntegerCache.low)];
  return new Integer(i);
}
```

 编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来
创建，那么就会引用相同的对象。
```Java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

#### 2.3、在 Java 8 中，Integer 缓存池的大小默认为 -128~127。
```Java
private static class IntegerCache {  
  static final int low = -128;
  static final int high;
  static final Integer cache[];
  static {
  // high value may be configured by property
  int h = 127;
  String integerCacheHighPropValue =
  sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
  if (integerCacheHighPropValue != null) {
    try {
    int i = parseInt(integerCacheHighPropValue);
    i = Math.max(i, 127);
    // Maximum array size is Integer.MAX_VALUE
    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
    } catch( NumberFormatException nfe) {
    // If the property cannot be parsed into an int, ignore it.
    }
  }
  high = h;
  cache = new Integer[(high - low) + 1];
  int j = low;
  for(int k = 0; k < cache.length; k++)
    cache[k] = new Integer(j++);
  // range [-128, 127] must be interned (JLS7 5.1.7)
  assert IntegerCache.high >= 127;
  }
}
```

#### 2.4、基本类型对应的缓冲池如下：

数据类型 | 缓冲池
---|---
boolean | true and false
byte | all byte values
short | -128 and 127
int | -128 and 127
char | \u0000 to \u007F

在使用这些基本类型对应的包装类型时，如果该数值范围在缓冲池范围内，就可以直接使用缓冲池中的对象。

#### 2.5、Integer缓冲池的上限是否可以调整  
在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，
在启动 jvm 的时候，通过` -XX:AutoBoxCacheMax=<size>`来指定这个缓冲池的大小，
该选项在 JVM 初始化的时候会设定一个名为 `java.lang.IntegerCache.high` 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。
```
private static class IntegerCache {
  // ....
  static {
    // high value may be configured by property
    int h = 127;
    String integerCacheHighPropValue =
    sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
    if (integerCacheHighPropValue != null) {
    try {
      int i = parseInt(integerCacheHighPropValue);
      i = Math.max(i, 127);
      // Maximum array size is Integer.MAX_VALUE
      h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
    } catch( NumberFormatException nfe) {
      // If the property cannot be parsed into an int, ignore it.
    }
    }
    high = h;
    // ....
  }
}
```

## 3 关键字
#### 3.1、final关键字：
**3.1.1、修饰数据：**  
声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。
对于基本类型，final 使数值不变；  
对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。
```Java
// 修饰基本数据类型：
final int x = 1;
// x = 2; // cannot assign value to final variable 'x'
// 修饰引用类型
final A y = new A();
y.a = 1;
```
**3.1.2、修饰方法：**  
声明方法不能被子类重写。
private 方法隐式地被指定为 final，如果在子类中定义的方法和基类中的一个 private 方法签名相同，此时子类的方法不是重写基类方法，而是在子类中定义了一个新的方法。  
3、修饰类：  
声明类不允许被继承，称为最终类，如String。

#### 3.2、static关键字：
**3.2.1、静态变量：**  
* 静态变量：又称为类变量，也就是说这个变量属于类的，类所有的实例都共享静态变量，可以直接通过类名来
访问它。静态变量在内存中只存在一份。  
* 实例变量：每创建一个实例就会产生一个实例变量，它与该实例生命周期相同
```Java
public class A {
  private int x; // 实例变量
  private static int y; // 静态变量
  public static void main(String[] args) {
    // int x = A.x; // Non-static field 'x' cannot be referenced from a static context
    A a = new A();
    int x = a.x;
    int y = A.y;
  }
}
```
**3.2.2、静态方法：**  
静态方法在类加载的时候就存在了，它不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。
```Java
public abstract class A {
  public static void func1(){
  }
  // public abstract static void func2(); 
  // Illegal combination of modifiers: 'abstract' and 'static'
}
```
只能访问所属类的静态字段和静态方法，方法中不能有 this 和 super 关键字。
```Java
public class A {
	private static int x;
	private int y;
	public static void func1(){
		int a = x;
		// int b = y; // Non-static field 'y' cannot be referenced from a static context
		// int b = this.y; // 'A.this' cannot be referenced from a static context
		// int b = super.y; // 'A.super' cannot be referenced from a static context
	}
}
```
**3.2.3、静态语句块：**  
静态语句块在类初始化时运行一次。执行的顺序与代码顺序一致。
```Java
public class A {
  static {
    System.out.println("123");
  }
  public static void main(String[] args) {
    A a1 = new A();
    A a2 = new A();
  }
}
// --- 执行结果 ---
// 123
```
**3.2.4、静态内部类：**  
非静态内部类依赖于外部类的实例，而静态内部类不需要。  
静态内部类不能访问外部类的非静态的变量和方法。
```Java
public class OuterClass {
  class InnerClass {  }
  static class StaticInnerClass { }
  public static void main(String[] args) {
    // InnerClass innerClass = new InnerClass();  // 'OuterClass.this' cannot be referenced from a static context
    OuterClass outerClass = new OuterClass();
    InnerClass innerClass = outerClass.new InnerClass();
    StaticInnerClass staticInnerClass = new StaticInnerClass();
  }
}
```
**3.2.5、静态导包：**  
在使用静态变量和方法时不用再指明 ClassName，从而简化代码，但可读性大大降低，不推荐。
```
import static com.xxx.ClassName.*
```
**3.2.6、初始化顺序：**  
静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。
```
public static String staticField = "静态变量";

static {
  System.out.println("静态语句块");
}

public String field = "实例变量";

{
  System.out.println("普通语句块");
}

// 最后才是构造函数的初始化。
public InitialOrderTest() {
  System.out.println("构造函数");
}
```
存在继承的情况下，初始化顺序为：  
```
父类（静态变量、静态语句块）
子类（静态变量、静态语句块）
父类（实例变量、普通语句块）
父类（构造函数）
子类（实例变量、普通语句块）
子类（构造函数）
```

## 4 反射
#### 4.1、反射是什么：
每个类都有一个 Class 对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 .class 文件，该文件内容保存着 Class 对象。

类加载相当于 Class 对象的加载，类在第一次使用时才动态加载到 JVM 中。也可以使用`
Class.forName("com.mysql.jdbc.Driver")`这种方式来控制类的加载，该方法会返回一个 Class 对象。

反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 .class 不存在也可以加载进来。

#### 4.2、java.lang.reflect：
Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：  
```
Field ：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；  
Method ：可以使用 invoke() 方法调用与 Method 对象关联的方法；  
Constructor ：可以用 Constructor 创建新的对象。  
```

#### 4.3、反射的优点：
```
1、可扩展性 ：应用程序可以利用全限定名创建可扩展对象的实例，来使用来自外部的用户自定义类。

2、类浏览器和可视化开发环境：一个类浏览器需要可以枚举类的成员。
可视化开发环境（如 IDE）可以从利用反射中可用的类型信息中受益，
以帮助程序员编写正确的代码。

3、调试器和测试工具：调试器需要能够检查一个类里的私有成员。
测试工具可以利用反射来自动地调用类里定义的可被发现的 API 定义，
以确保一组测试中有较高的代码覆盖率。
```
#### 4.4、反射的缺点：  
尽管反射非常强大，但也不能滥用。如果一个功能可以不用反射完成，那么最好就不用。在我们使用反射技术时，下面几条内容应该牢记于心。
```
1、性能开销 ：反射涉及了动态类型的解析，所以 JVM 无法对这些代码进行优化。
因此，反射操作的效率要比那些非反射操作低得多。
我们应该避免在经常被执行的代码或对性能要求很高的程序中使用反射。

2、安全限制 ：使用反射技术要求程序必须在一个没有安全限制的环境中运行。
如果一个程序必须在有安全限制的环境中运行，如 Applet，那么这就是个问题了。

3、内部暴露 ：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），
所以使用反射可能会导致意料之外的副作用，这可能导致代码功能失调并破坏可移植性。
反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。
```
[Trail: The Reflection API](https://docs.oracle.com/javase/tutorial/reflect/index.html)  
[深入解析 Java 反射（1）- 基础](http://www.sczyh30.com/posts/Java/java-reflection-1/)