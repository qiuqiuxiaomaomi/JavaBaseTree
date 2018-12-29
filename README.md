# JavaBaseTree
Java基础技术研究


<pre>
Java中isAssignableFrom和instanceof

     示例：Class1.isAssignableFrom(Class2)
     解释：① 判断Class1和Class2是否相同。②Class1是否是Class2的父类或者接口。


     instanceof
     示例：object   instanceof   TypeName
     解释：判断一个对象实例是否是一个类或接口的或其子类子接口的实例
</pre>

![](https://i.imgur.com/2FlpX4P.png)

<pre>
Java对象的表示模型和运行时内存表示
  
      Hotspot主要是用C++写的，所以它定义的Java对象表示模型也是基于C++实现的。

      Java对象的表示模型叫做“OOP-Klass”二分模型，包括两部分:
      1. OOP，即Ordinary Object Point，普通对象指针。其实就是表示对象的实例信息
      2. Klass，即Java类的C++对等体，用来描述Java类，包含了元数据和方法信息等

      一个Java对象就包括两部分，数据和方法，分别对应到OOP和Klass。

      JVM运行时加载一个Class时，会在JVM内部创建一个instanceKlass对象，表示这个类的运行时元数据。创建一个这个Class的Java对象时，会在JVM内部相应的创建一个instanceOop来表示这个Java对象。instanceKlass对象放在了方法区，instanceOop放在了堆，instanceOop的引用放在了JVM栈。

      JVM是基于栈来运行的，当一个线程调用一个对象的方法时，会在它的JVM栈的栈顶创建一个栈
      帧（Frame）的数据结构，这个数据结构是用来保存方法的局部变量，操作数栈，动态连接和方
      法返回值的。通过参数传递的值和在方法中new出来的对象的引用都保持在局部变量表里面

      Java的方法调用是值传递，不是引用传递，原因就在这里，传递进来的参数相当于在局部变量
      表里面拷贝了一份，实际计算时，操作数栈操作的是局部变量变量里面的值，而不是外部的变量

      在堆中创建的Java对象实际只包含数据信息，它包含三部分：
          1. 对象头，也叫Mark Word
          2. 元数据指针，可以理解为类对象指针，指向方法区的instanceKlass实例
          3. 实例数据

          如果是数据对象的话，还多了一个部分，就是数组长度。

          对象头主要存储对象运行时记录信息，如hashcode, GC分代年龄，锁状态标志，偏向线
          程ID，偏向时间戳等。对象头的长度和JVM的字长一致，比如32位JVM的对象头是32位，
          64位JVM的对象头是64位。

          这里可以看到，所谓的给一个对象加锁，其实就是设置了对象头某些位。当其他线程看到
          这个对象的状态是加锁状态后，就等待释放锁

       在方法区的instanceKlass对象相当于Class加载后创建的运行时对象，它包含了运行时常
       量池，字段，方法等元数据，当调用一个对象的方法时，如上面的图所示，实际定位到了方法
       区的instanceKlass对象的方法元数据
</pre>

<pre>
Java中创建对象的5中方法
      1）
      Employee emp1 = new Employee();  
      
      2）
      Employee emp2 = (Employee) Class.forName("com.exa.Employee").newInstance();
      
      3）
      Constructor<Employee> constructor = Employee.class.getConstructor();  
      Employee emp3 = constructor.newInstance();  

      5）
      Employee emp4 = (Employee) emp3.clone();  

      6）
      ObjectInputStream in = new ObjectInputStream(new FileInputStream("data.obj"));  
      Employee emp5 = (Employee) in.readObject();  
</pre>

![](https://i.imgur.com/4DJgsB0.png)

<pre>
JAVA类加载---双亲委派模型

    public Class<?> loadClass(String name)throws ClassNotFoundException {
            return loadClass(name, false);
    }
    
    protected synchronized Class<?> loadClass(String name, boolean resolve)throws ClassNotFoundException {
            // 首先判断该类型是否已经被加载
            Class c = findLoadedClass(name);
            if (c == null) {
                //如果没有被加载，就委托给父类加载或者委派给启动类加载器加载
                try {
                    if (parent != null) {
                         //如果存在父类加载器，就委派给父类加载器加载
                        c = parent.loadClass(name, false);
                    } else {
                    //如果不存在父类加载器，就检查是否是由启动类加载器加载的类，通过调用本地方法native Class findBootstrapClass(String name)
                        c = findBootstrapClass0(name);
                    }
                } catch (ClassNotFoundException e) {
                 // 如果父类加载器和启动类加载器都不能完成加载任务，才调用自身的加载功能
                    c = findClass(name);
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
</pre>

![](https://i.imgur.com/oFBC3X9.png)

<pre>
Java类加载过程

     验证：
         这一阶段的主要目的是为了确保Class文件的字节流中包含的信息是否符合当前虚拟机的
     要求，并且不会危害虚拟机自身的安全

     准备：
         准备阶段是正式为类变量分配内存并设置类变量的初始值阶段，即在方法区中分配这些变量
     所使用的内存空间

     解析：
         解析阶段是指虚拟机将常量池中的符号引用替换为直接引用的过程

     初始化：
         

     使用：
     卸载：
</pre>

<pre>
JAVA中Long与Integer

     Long与Integer都将 -128~127 这些对象缓存了;
     
     LongCache类
     IntegerCache类
</pre>

![](https://i.imgur.com/O1cyHZA.png)

<pre>
Hash与Hash扩容

     HashMap扩容

     1）HashTable
     2) HashMap
     3) TreeMap
     5) LinkedHashMap
</pre>

<pre>
Transient关键字
      一个对象只要实现了Serilizable接口，这个对象就可以被序列化，java的这种序列化模式为
   开发者提供了很多便利，可以不必关系具体序列化的过程，只要这个类实现了Serilizable接口，
   这个的所有属性和方法都会自动序列化。但是有种情况是有些属性是不需要序列号的，所以就用到
   这个关键字。只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序
   列化对象的时候，这个属性就不会序列化到指定的目的地中
</pre>

![](https://i.imgur.com/jeSwqKV.png)

<pre>
      Vector和ArrayList
           1，vector是线程同步的，所以它也是线程安全的，而arraylist是线程异步的，是不
              安全的。如果不考虑到线程的安全因素，一般用arraylist效率比较高。

　　        2，如果集合中的元素的数目大于目前集合数组的长度时，vector增长率为目前数组长
              度的100%，而arraylist增长率为目前数组长度的50%。如果在集合中使用数据量比较大的数据，用vector有一定的优势

     arraylist和linkedlist
　　        1.ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。

　　        2.对于随机访问get和set，ArrayList觉得优于LinkedList，因为LinkedList要移动指针。

     HashMap与TreeMap
　　        1、 HashMap通过hashcode对其内容进行快速查找，而TreeMap中所有的元素都保持着
              某种固定的顺序，如果你需要得到一个有序的结果你就应该使用TreeMap（HashMap中元素的排列顺序是不固定的）。

     HashTable与HashMap
　　        1、同步性:Hashtable是线程安全的，也就是说是同步的，而HashMap是线程序不安全的，不是同步的。

　　        2、HashMap允许存在一个为null的key，多个为null的value 。

　　        3、hashtable的key和value都不允许为null。
</pre>

<pre>
指令重排序， CPU重排序

      对主存的一次访问一般花费硬件的数百次时钟周期。处理器通过缓存（caching）能够从数量级
      上降低内存延迟的成本这些缓存为了性能重新排列待定内存操作的顺序。也就是说，程序的读写
      操作不一定会按照它要求处理器的顺序执行

      重排序的种类
          1）编译期重排。编译源代码时，编译器依据对上下文的分析，对指令进行重排序，以之更适合于CPU的并行执行。
          2）运行期重排，CPU在执行过程中，动态分析依赖部件的效能，对指令做重排序优化

      指令重排序遵循的规则
          编译器和处理器可能会对操作做重排序，但是要遵守数据依赖关系，编译器和处理器不会
      改变存在数据依赖关系的两个操作的执行顺序。如果两个操作访问同一个变量，且这两个操作中
      有一个为写操作，此时这两个操作之间就存在数据依赖性。数据依赖分下列三种类型：

      名称 代码示例说明
      写后读 a = 1;b = a;写一个变量之后，再读这个位置。
      写后写 a = 1;a = 2;写一个变量之后，再写这个变量。
      读后写 a = b;b = 1;读一个变量之后，再写这个变量。

      上面三种情况，只要重排序两个操作的执行顺序，程序的执行结果将会被改变。像这种有直接依
      赖关系的操作，是不会进行重排序的


      CPU重排序
           现在的CPU一般采用流水线来执行指令。一个指令的执行被分成：取指、译码、访存、执行、
      写回、等若干个阶段。然后，多条指令可以同时存在于流水线中，同时被执行。指令流水线并不
      是串行的，并不会因为一个耗时很长的指令在“执行”阶段呆很长时间，而导致后续的指令都
     卡在“执行”之前的阶段上。相反，流水线是并行的，多个指令可以同时处于同一个阶段，只要
     CPU内部相应的处理部件未被占满即可。比如说CPU有一个加法器和一个除法器，那么一条加法指
     令和一条除法指令就可能同时处于“执行”阶段, 而两条加法指令在“执行”阶段就只能串行工作。

     然而，这样一来，乱序可能就产生了。比如一条加法指令原本出现在一条除法指令的后面，但是
     由于除法的执行时间很长，在它执行完之前，加法可能先执行完了。再比如两条访存指令，可能
     由于第二条指令命中了cache而导致它先于第一条指令完成。一般情况下，指令乱序并不是CPU在
     执行指令之前刻意去调整顺序。CPU总是顺序的去内存里面取指令，然后将其顺序的放入指令流
     水线。但是指令执行时的各种条件，指令与指令之间的相互影响，可能导致顺序放入流水线的
     指令，最终乱序执行完成。这就是所谓的“顺序流入，乱序流出”

     相比于CPU的乱序，编译器的乱序才是真正对指令顺序做了调整。但是编译器的乱序也必须保证程
     序上下文的因果关系不发生改变
</pre>

<pre>
final
        根据程序上下文环境，Java关键字final有“这是无法改变的”或者“终态的”含义，它可以修饰非抽象类、非抽象类成员方法和变量。你可能出于两种理解而需要阻止改变：设计或效率。
        final类不能被继承，没有子类，final类中的方法默认是final的。
        final方法不能被子类的方法覆盖，但可以被继承。
        final成员变量表示常量，只能被赋值一次，赋值后值不再改变。
        final不能用于修饰构造方法。
        注意：父类的private成员方法是不能被子类方法覆盖的，因此private类型的方法默认是final类型的

static表示“全局”或者“静态”的意思，用来修饰成员变量和成员方法，也可以形成静态static代码
        块，但是Java语言中没有全局变量的概念。

        被static修饰的成员变量和成员方法独立于该类的任何对象。也就是说，它不依赖类特定的
        实例，被类的所有实例共享。只要这个类被加载，Java虚拟机就能根据类名在运行时数据区
        的方法区内定找到他们。因此，static对象可以在它的任何对象创建之前访问，无需引用任何对象。
</pre>

<pre>
Class.forName和classloader

      Class.forName(className)方法，内部实际调用的方法是  Class.forName(className,true,classloader);

      第2个boolean参数表示类是否需要初始化，  Class.forName(className)默认是需要初始化。

      一旦初始化，就会触发目标对象的 static块代码执行，static参数也也会被再次初始化。

     
      ClassLoader.loadClass(className)方法，内部实际调用的方法
      是  ClassLoader.loadClass(className,false);

      第2个 boolean参数，表示目标对象是否进行链接，false表示不进行链接，由上面介绍可以，

      不进行链接意味着不进行包括初始化等一些列步骤，那么静态块和静态对象就不会得到执行
</pre>

<pre>
string、stringbuilder、stringbuffer区别

      String：
             因为有“final”修饰符，所以可以知道string对象是不可变的。
　　　　      private final char value[];

      StringBuilder与StringBuffer：
             都继承自AbstractStringBuilder类；
             在AbstractStringBuilder中也是使用字符数组保存字符串，可知这两种对象都是可变的。
　　　　      char[] value;

      String类型是线程安全的;
      StringBuffer对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的;
      StringBuilder并没有对方法进行加同步锁，所以是非线程安全的
</pre>

<pre>
Hash冲突解决方法：
      1）拉链法
      2）线性探测再散列
</pre>

<pre>
Java 对象生命周期

      1) 创建阶段
      2）应用阶段
      3）不可见阶段
      5）不可达阶段
      6）收集阶段
      7）终结阶段
      8）对象空间重新分配阶段
</pre>

线程非安全

![](https://i.imgur.com/iDQg9mP.png)

线程安全

![](https://i.imgur.com/1X9SvOs.png)

<pre>
SimpleDateFormat的线程安全性

      DateFormat, SimpleDateFormat类都不是线程安全的。

      在parse方法的最后，会调用CalendarBuilder的establish方法，入参就
      是SimpleDateFormat维护的Calendar实例，在establish方法中会调用calendar的
      clear方法。

      可知SimpleDateFormat维护的用于format和parse方法计算日期-时间的calendar被清
      空了，如果此时线程A将calendar清空且没有设置新值，线程B也进入parse方法用
      到了SimpleDateFormat对象中的calendar对象，此时就会产生线程安全问题
</pre>

<pre>
sleep(),wait()区别:

      1)父类不同，sleep()来自Thread, wait()来自Object
      2)sleep()没有释放锁，wait()释放了锁。
        sleep()不换出系统资源，wait（）进入等待对垒，其他线程可以使用CPU资源。
      3）使用范围
            wait(),notify()只能同步空值或者同步控制块内，而sleep（）可以在任何地方使用。
            sleep()必须捕获异常而wait(),notify()不需要捕获异常。
</pre>

<pre>
经典问题：

      package com.paddx.test.string;
		public class StringTest {
		    public static void main(String[] args) {
		        String str1 = "string";
		        String str2 = new String("string");
		        String str3 = str2.intern();
		 
		        System.out.println(str1==str2);//#1
		        System.out.println(str1==str3);//#2
		    }
		}

      结果 #1：因为str1指向的是字符串中的常量，str2是在堆中生成的对象，所以str1==str2
              返回false。

      结果 #2：str2调用intern方法，会将str2中值（“string”）复制到常量池中，但是常量池
      中已经存在该字符串（即str1指向的字符串），所以直接返回该字符串的引用，因
      此str1==str2返回true。

      package com.paddx.test.string;
      public class StringTest01 {
      public static void main(String[] args) {
	        String baseStr = "baseStr";
	        final String baseFinalStr = "baseStr";
	 
	        String str1 = "baseStr01";
	        String str2 = "baseStr"+"01";
	        String str3 = baseStr + "01";
	        String str4 = baseFinalStr+"01";
	        String str5 = new String("baseStr01").intern();
	 
	        System.out.println(str1 == str2);//#3
	        System.out.println(str1 == str3);//#4
	        System.out.println(str1 == str4);//#5
	        System.out.println(str1 == str5);//#6
      }
      }

      结果#3：str1==str2 肯定会返回true，因为str1和str2都指向常量池中的同一引用地址。
             所以其实在JAVA 1.6之后，常量字符串的“+”操作，编译阶段直接会合成为一个字符串。
      结果 #4：因为str3实际上是stringBuilder.append()生成的结果，所以与str1不相等，
             结果 返回false。
      结果 #5 ：因为str1和str4指向的都是常量池中的第三项，所以str1==str4返回true。这里
             我们还能发现一个现象，对于final字段，编译期直接进行了常量替换，而对于非
             final字段则是在运行期进行赋值处理的。
      结果 #6 ：因为str5和str1都指向的都是常量池中的同一个字符串，所以str1==str5返回true。


      package com.paddx.test.string;　　
		public class InternTest {
		    public static void main(String[] args) {
		 
		        String str2 = new String("str")+new String("01");
		        str2.intern();
		        String str1 = "str01";
		        System.out.println(str2==str1);//#7
		    }
		}


      package com.paddx.test.string;
 
		public class InternTest01 {
		    public static void main(String[] args) {
		        String str1 = "str01";
		        String str2 = new String("str")+new String("01");
		        str2.intern();
		        System.out.println(str2 == str1);//#8
		    }
		}

      JDK 1.7后，HotSpot 将常量池从永久代移到了元空间，正因为如此，JDK 1.7 后的intern
      方法在实现上发生了比较大的改变，JDK 1.7后，intern方法还是会先去查询常量池中是否有
      已经存在，如果存在，则返回常量池中的引用，这一点与之前没有区别，区别在于，如果在常
      量池找不到对应的字符串，则不会再将字符串拷贝到常量池，而只是在常量池中生成一个对原
      字符串的引用。所以:

      结果 #7：在第一种情况下，因为常量池中没有“str01”这个字符串，所以会在常量池中生成一个对堆中的“str01”的引用，而在进行字面量赋值的时候，常量池中已经存在，所以直接返回该引用即可，因此str1和str2都指向堆中的字符串，返回true。

      结果 #8：调换位置以后，因为在进行字面量赋值（String str1 = “str01″）的时候，常量池中不存在，所以str1指向的常量池中的位置，而str2指向的是堆中的对象，再进行intern方
      法时，对str1和str2已经没有影响了，所以返回false。

      三、常见面试题解答

		有了对以上的知识的了解，我们现在再来看常见的面试或笔试题就很简单了：
		
		Q：String s = new String(“xyz”)，创建了几个String Object?
		
		A：两个，常量池中的”xyz”和堆中对象。
		
		Q：下列程序的输出结果：
		
		String s1 = “abc”;
		String s2 = “abc”;
		System.out.println(s1 == s2);
		
		A：true，均指向常量池中对象。
		
		Q：下列程序的输出结果：
		
		String s1 = new String(“abc”);
		String s2 = new String(“abc”);
		System.out.println(s1 == s2);
		
		A：false，两个引用指向堆中的不同对象。
		
		Q：下列程序的输出结果：
		
		String s1 = “abc”;
		String s2 = “a”;
		String s3 = “bc”;
		String s4 = s2 + s3;
		System.out.println(s1 == s4);
		
		A：false，因为s2+s3实际上是使用StringBuilder.append来完成，会生成不同的对象。
		
		Q：下列程序的输出结果：
		
		String s1 = “abc”;
		final String s2 = “a”;
		final String s3 = “bc”;
		String s4 = s2 + s3;
		System.out.println(s1 == s4);
		
		A：true，因为final变量在编译后会直接替换成对应的值，所以实际上等于s4=”a”+”bc”，而这种情况下，编译器会直接合并为s4=”abc”，所以最终s1==s4。
		
		Q：下列程序的输出结果：
		
		String s = new String(“abc”);
		String s1 = “abc”;
		String s2 = new String(“abc”);
		
		System.out.println(s == s1.intern());
		System.out.println(s == s2.intern());
		System.out.println(s1 == s2.intern());
		
		A：false，false，true，具体原因参考第二部分内容。
</pre>