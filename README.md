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