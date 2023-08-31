# CoreJava

---

### 1. 类初始化的顺序

1. 按顺序初始化父静态变量和静态方法块

2. 按顺序初始化子静态变量和静态方法块

3. 按顺序初始化父成员变量和普通方法块

4. 初始化父构造函数

5. 按顺序初始化子成员变量和普通方法块

6. 初始化子构造函数

---

### 2. 按位运算符

* 只有 >> 有符号右移动 符号为负时 高位补1 其他情况都是补0

```java
/**
 * 左移位 低位补 0
 */ 
// 1010 << 10100
Integer.toBinaryString(10) + " << " + Integer.toBinaryString(10<<1);

/**
 * 有符号右移位 值为正在高位补0 值为负在高位补1
 */ 
// 1010 >> 101
Integer.toBinaryString(10) + " >> " + Integer.toBinaryString(10>>1);
// 11111111111111111111111111110110 >> 11111111111111111111111111111011
Integer.toBinaryString(-10) + " >> " + Integer.toBinaryString(-10>>1);

/**
 * 无符号右移位 高位补0
 */ 
// 1010 >>> 101
Integer.toBinaryString(10) + " >>> " + Integer.toBinaryString(10>>>1);
// 11111111111111111111111111110110 >>> 1111111111111111111111111111011
Integer.toBinaryString(-10) + " >>> " + Integer.toBinaryString(-10>>>2);

/**
 * 左移位相当于乘以2的n次方 n位移动位数
 */
// 10 << 160
Integer.toString(10) + " << " + Integer.toString(10 << 4);

/**
 * 右移位相当于除以2的n次方 n位移动位数
 */
// 10 >>> 4
Integer.toString(10) + " >>> " + Integer.toString(16 >>> 2);
```

---

### 3. 内部类

1. 为什么成员内部类可以无条件访问外部类的成员？
   
   编译器会默认为成员内部类添加了一个指向外部类对象的引用，编译器会对构造函数默认添加一个参数，该参数的类型为指向外部类对象的一个引用，所以成员内部类中的引用便指向了外部类对象。

2. 为什么局部内部类和匿名内部类只能访问局部final变量？
   
   编译器默认会在匿名内部类（局部内部类）的常量池中添加一个内容相等的字面量或直接将相应的字节码嵌入到执行字节码中。这样一来，匿名内部类使用的变量是另一个局部变量，只不过值和方法中局部变量的值相等，因此和方法中的局部变量完全独立开。

---

### 4. Clone

1. 重写clone方法必须实现Cloneable接口。

2. clone方法是浅拷贝，对象内属性引用的对象只会拷贝引用地址，而不会将引用的对象重新分配内存，相对应的深拷贝则会连引用的对象也重新创建。

---

### 5. 强软弱虚引用

#### 5.1 强引用

* 强引用是最传统的"引用"的定义，是指在程序代码之中普遍存在的引用赋值，即类似"Object obj=new Object()"这种引用关系。无论任何情况下，只要强引用关系还存在，垃圾收集器就永远不会回收掉被引用的对象。

#### 5.2 软引用

* 软引用是用来描述一些还有用，但非必须的对象。在内存不够时回收软引用指向的对象。在JDK 1.2版之后提供了SoftReference类来实现软引用。

* 使用场景：大对象的本地缓存(如图片)。

#### 5.3 弱引用

* 弱引用也是用来描述那些非必须对象，但是它的强度比软引用更弱一些，被弱引用关联的对象只能生存到下一次垃圾收集发生为止。当垃圾收集器开始工作，无论当前内存是否足够，都会回收掉只被弱引用关联的对象。在JDK 1.2版之后提供了WeakReference类来实现弱引用。

* 使用场景：ThreadLocalMap中的kv使用弱引用关联。
  
  <img title="" src="file:///E:/markdown/images/weakReference.png" alt="class文件格式" width="610">

#### 5.4 虚引用

* 无法通过虚引用来取得一个对象实例。虚引用与软引用和弱引用的一个区别在于：虚引用必须和引用队列（ReferenceQueue）联合使用。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。在JDK 1.2版之后提供了PhantomReference类来实现虚引用。

* 使用场景：堆外内存
  
  <img title="" src="file:///E:/markdown/images/phantomReference.png" alt="class文件格式" width="613">

---

### 6. 反射

##### 6.1 反射

* 反射就是把java类中的各种成分映射成一个个的Java对象。Class类的实例表示java运行时的类(class ans enum)或接口(interface and annotation)（每个java类运行时都在JVM里表现为一个class对象，可通过类名.class、类型.getClass()、Class.forName("类名")等方法获取class对象）。

* 在Java中，Class类与java.lang.reflect类库一起对反射技术进行了全力的支持。在反射包中，我们常用的类主要有Constructor类表示的是Class 对象所表示的类的构造方法，利用它可以在运行时动态创建对象、Field表示Class对象所表示的类的成员变量，通过它可以在运行时动态修改成员变量的属性值(包含private)、Method表示Class对象所表示的类的成员方法，通过它可以动态调用对象的方法(包含private)，下面将对这几个重要类进行分别说明。

```java
// 获取class的3中方式
Class clazz1 = Class.forName("com.demo.coreJava.reflect.Person");
Class clazz2 = Person.class;
Class clazz3 = person.getClass();

// 反射获取实例(类包含无参构造函数)
Person person1 = (Person) clazz1.newInstance();
// 反射获取实例(类不包含无参构造函数)
Constructor constructor = clazz1.getConstructor(int.class, String.class);
Person person2 = (Person)constructor.newInstance(10, "hello");

// 操作成员变量
Field field = person1.getClass().getDeclaredField("name");
// 设为true才能访问私有变量
field.setAccessible(true);
field.set(person1, "funking");

// 操作方法
Method method = person1.getClass().getDeclaredMethod("setName", String.class);
method.invoke(person1, "method");
```

##### 6.2 动态代理

* java动态代理方式：jdk动态代理，cglib(spring aop，mybatis使用)，javassit(dubbo使用)：
  
  1. jdk动态代理
     
     ```java
     public class ProxyHandler implements InvocationHandler {
         private Object o;
         public ProxyHandler(Object o) {
             this.o = o;
         }
         @Override
         public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
             method.invoke(o, args);
             return null;
         }
         public static void main(String[] args) {
             ByeInterface bye1 = (ByeInterface) Proxy
                  .newProxyInstance(ByeInterface.class.getClassLoader(), 
                                    new Class[] { ByeInterface.class },
                                    new ProxyHandler(new Bye()));
              bye1.sayBye();
         }
     }
     ```
  
  2. cglib动态代理
     
     cglib是code generate library，代码生成库。底层是通过asm操作字节码来生成代理类cglib不能对final类进行代理操作。
     
     <img title="" src="file:///E:/markdown/images/cglib.png" alt="class文件格式" width="510">
     
     ```java
     public class MyInterceptor implements MethodInterceptor {
         @Override
         public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
             Object obj = methodProxy.invokeSuper(o, objects);
             return obj;
         }
         public static void main(String[] args) {
             Enhancer enhancer = new Enhancer();
             enhancer.setSuperclass(Bye.class);
             enhancer.setCallback(new MyInterceptor());
             // 过滤器,返回第几个拦截器
             enhancer.setCallbackFilter((method -> {
                 return 0;
             }));
             Bye bye = (Bye) enhancer.create();
             bye.sayBye();
         }
     }
     ```

* jdk动态代理与cglib区别
  
  1. jdk只能对接口进行代理(所有生成的代理类父类为Proxy)，cglib可以对类进行代理。
  
  2. Java动态代理使用Java原生的反射API进行操作，在生成类上比较高效；CGLIB使用ASM框架直接对字节码进行操作，在类的执行过程中比较高效。

---

### 7. 集合

<img title="" src="file:///E:/markdown/images/collections.png" alt="class文件格式" width="652">

##### 7.1 List（有序，可重复）

| 集合         | 是否有序 | 可重复 | 线程安全 | 值为空 | 实现              |
| ---------- | ---- | --- | ---- | --- | --------------- |
| Vector     | 有序   | 可重复 | 安全   | 可空  | 数组+synchronized |
| ArrayList  | 有序   | 可重复 | 不安全  | 可空  | 数组              |
| LinkedList | 有序   | 可重复 | 不安全  | 可空  | 链表              |

##### 7.2 Set（无序，不可重复）

| 集合            | 是否有序 | 可重复  | 线程安全 | 值为空 | 实现                |
| ------------- | ---- | ---- | ---- | --- | ----------------- |
| HashSet       | 无序   | 不可重复 | 不安全  | 可空  | HashMap key       |
| LinkedHashSet | 无序   | 不可重复 | 不安全  | 可空  | LinkedHashMap key |
| TreeSet       | 有序   | 不可重复 | 不安全  | 不可空 | TreeMap key       |

LinkedHash可按照插入顺序迭代

##### 7.3 Map（无序，key不可重复）

| 集合            | 是否有序 | 可重复     | 线程安全 | 值为空      | 实现                 |
| ------------- | ---- | ------- | ---- | -------- | ------------------ |
| HashMap       | 无序   | key不可重复 | 不安全  | 可空       | 数组+链表(红黑树)         |
| LinkedHashMap | 无序   | key不可重复 | 不安全  | 可空       | 数组+链表(红黑树)         |
| TreeMap       | 有序   | key不可重复 | 不安全  | k不可空,v可空 | 红黑树                |
| HashTable     | 无序   | key不可重复 | 安全   | 不可空      | 数组+链表+synchronized |

* HashMap.put()方法：负载因子 loadFactor=0.75，初始容量 initialCapacity=16，table第一次put时候初始化长度为初始容量(默认为16，如果指定则为最靠近指定值得2得n次方)，以后每当大于容量*负载因子则扩大两倍。
  
  <img title="" src="file:///E:/markdown/images/hashmapPutMethod.png" alt="class文件格式" width="652">

* 在jdk1.8版本后，java对HashMap做了改进，在链表长度大于8的时候，数据存在红黑树中，以加快检索速度。

* hashtable key value 不可为空，出于线程安全性考虑。

##### 7.4 Queue（队列）

|     | 抛出异常      | 返回特殊值    |
| --- | --------- | -------- |
| 插入  | add(e)    | offer(e) |
| 移除  | remove()  | poll()   |
| 检查  | element() | peek()   |

##### 7.5 Deque（双端队列）

|     | 第一个元素（头部）     | 第一个元素（头部）     | 最后一个元素（尾部）   | 最后一个元素（尾部）   |
| --- | ------------- | ------------- | ------------ | ------------ |
|     | 抛出异常          | 特殊值           | 抛出异常         | 特殊值          |
| 插入  | addFirst(e)   | offerFirst(e) | addLast(e)   | offerLast(e) |
| 移除  | removeFirst() | pollFirst()   | removeLast() | pollLast()   |
| 检查  | getFirst()    | peekFirst()   | getLast()    | peekLast()   |

| Queue 方法  | 等效 Deque 方法   |
| --------- | ------------- |
| add(e)    | addLast(e)    |
| offer(e)  | offerLast(e)  |
| remove()  | removeFirst() |
| poll()    | pollFirst()   |
| element() | getFirst()    |
| peek()    | peekFirst()   |

| 堆栈方法    | 等效 Deque 方法   |
| ------- | ------------- |
| push(e) | addFirst(e)   |
| pop()   | removeFirst() |
| peek()  | peekFirst()   |

##### 7.6 比较器

* Comparable：内部比较器，集合元素类型实现Comparable接口并重新compareTo方法，Collections.sort（T t）。

* Comparator：外部比较器，实现比较器并放入第二个参数，Collections.sort(T t, ? implement Comparator)。

---

### 8. jdk各版本特性

##### 8.1 Java8

1. 函数式编程
   
   * Lambda 表达式
   
   * 方法引用
   
   * stream & parallelStream
   
   * 四大内置函数接口Consumer，Function，Supplier，Predicate

2. Optional类
   
   这是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。

3. default方法
   
   接口中可以定义默认方法。

4. 类型注解

5. 重复注解

6. LocalDate/LocalDateTime

7. PermGen移除

8. StampedLock

##### 8.2 Java9

1. Java 平台 模块系统

2. Jshell

3. 集合、Stream 和 Optional

4. 进程 API

5. 平台日志 API 和 服务

6. 反应式流 （ Reactive Streams ）

7. 变量句柄

##### 8.3 Java10
