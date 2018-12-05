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