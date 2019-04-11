# 知识共享第2期
## 一、Swift的Class的初始化器介绍
### 一、初始化器
初始化器在创建特定类型的实例时被调用，就像java的构造函数。在这个简单的形式中，使用 init 关键字来写：
```
class AClass {
  var a: Int
  init(n: Int){ 
    //初始化
    a = n
  }
}
//定义一个AClass类型的变量
var aclass = AClass(n: 3)
```
### 二、默认初始化器
对于所有属性都有默认值，且没有继承其他类的类，Swift会提供一个默认初始化器，如：
```
class Shape {
  var width = 1
  var height = 2
}

var aShape = Shape()
```
### 三、 指定初始化器和便捷初始化器
Swift的类的初始化器分为指定初始化器和便捷初始化器。一个类至少有一个指定初始化器和零个及以上的便捷初始化器  
指定初始化器：
 ```
 init(parameters) { statement }
 ```
 便捷初始化器：
 ```
 convenience init(parameters) { statement }
 ```
 ### 四、初始化器调用规则
 1. 规则一  
 > 指定初始化器必须从它的直系父类调用指定初始化器。
 2. 规则二  
 > 便捷初始化器必须从相同的类里调用另一个初始化器。
 3. 规则三  
 > 便捷初始化器最终必须调用一个指定初始化器。
 第二条和第三条规则的意思是，便捷初始化器可以调用另一个便捷初始化器或指定初始化器。若它只调用了一个便捷初始化器，则它调用的便捷初始化器一定调用了指定初始化器
### 五、初始化过程
> Swift 的类初始化是一个两段式过程。在第一个阶段，每一个存储属性被引入类分配了一个初始值。一旦每个存储属性的初始状态被确定，第二个阶段就开始了，每个类都有机会在新的实例准备使用之前来定制它的存储属性。  

为保证两段式的初始化过程，Swift编译器提供了四种安全检查  
安全检查 1    
> 指定初始化器必须保证在向上委托给父类初始化器之前，其所在类引入的所有属性都要初始化完成。  

安全检查 2  
> 指定初始化器必须先向上委托父类初始化器，然后才能为继承的属性设置新值。如果不这样做，指定初始化器赋予的新值将被父类中的初始化器所覆盖。  

安全检查 3  
> 便捷初始化器必须先委托同类中的其它初始化器，然后再为任意属性赋新值（包括同类里定义的属性）。如果没这么做，便捷构初始化器赋予的新值将被自己类中其它指定初始化器所覆盖。  

安全检查 4  
> 初始化器在第一阶段初始化完成之前，不能调用任何实例方法、不能读取任何实例属性的值，也不能引用 self 作为值。

简而言之，类的初始化过程是先初始化子类中的存储属性，再初始化父类的存储属性，最终由便捷初始化器做一些额外的工作，也可以没有便捷初始化器。
### 更详细内容可参考
[Swift初始化](https://www.cnswift.org/initialization)





## 二、java泛型 
### 一、泛型的概念
> 泛型这个术语的意思是：“适用于许多许多的类型”，泛型在编程语言中出现时，其最初的目的是希望类或方法能够具备最广泛的表达能力，做到这一点便是通过解耦类或方法与所使用的类型之间的约束。

泛型实现了参数化类型的概念，使代码可以应用多种类型。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来具体的类型参数化（如构造方法*public&nbsp;Generic(String&nbsp;key)*），现定义成参数形式,可以称之为类型形参(*public&nbsp;Generic(T&nbsp;key)*)，然后在使用/调用时传入具体的类型即类型实参（*new&nbsp;Generic<String>("value")*）。

泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。


### 二、泛型的使用
泛型有三种使用方式，分别为：泛型类、泛型接口、泛型方法
#### 1、泛型类
泛型类型用于类的定义中，被称为泛型类。通过泛型可以完成对一组类的操作对外开放相同的接口。最典型的就是各种容器类，如：List、Set、Map。

定义最普通的泛型类：

```
//此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key;

    public Generic(T key) { //泛型构造方法形参key的类型也为T，T的类型由外部指定
        this.key = key;
    }

    public T getKey(){ //泛型方法getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}
```
具体使用
```
//泛型的类型参数只能是类类型（包括自定义类），不能是简单类型
//传入的实参类型需与泛型的类型参数类型相同，即为Integer.
Generic<Integer> genericInteger = new Generic<Integer>(123456);

//传入的实参类型需与泛型的类型参数类型相同，即为String.
Generic<String> genericString = new Generic<String>("key_vlaue");
Log.d("泛型测试","key is " + genericInteger.getKey());
Log.d("泛型测试","key is " + genericString.getKey());

```
定义的泛型类，如果不传入泛型类型实参的话，在泛型类中使用泛型的方法或成员变量定义的类型可以为任何的类型。


```
Generic generic = new Generic("111111");
Generic generic1 = new Generic(4444);
Log.d("泛型测试","key is " + generic.getKey());
Log.d("泛型测试","key is " + generic1.getKey());
```

```
D/泛型测试: key is 111111
D/泛型测试: key is 4444
```
#### 2、泛型接口
泛型接口与泛型类定义与使用基本相同

```
//定义一个泛型接口
public interface Generator<T> {
    public T next();
}
```
定义实现泛型接口的类，未传入泛型实参

```
/**
 * 未传入泛型实参时，与泛型类的定义相同，在声明类的时候，需将泛型的声明也一起加到类中
 * 即：class FruitGenerator<T> implements Generator<T>{
 * 如果不声明泛型，如：class FruitGenerator implements Generator<T>，编译器会报错："Unknown class"
 */
class FruitGenerator<T> implements Generator<T>{
    @Override
    public T next() {
        return null;
    }
}
```

定义实现泛型接口的类，传入泛型实参

```
/**
 * 传入泛型实参时：
 * 定义一个生产器实现这个接口,虽然我们只创建了一个泛型接口Generator<T>
 * 但是我们可以为T传入无数个实参，形成无数种类型的Generator接口。
 * 在实现类实现泛型接口时，如已将泛型类型传入实参类型，则所有使用泛型的地方都要替换成传入的实参类型
 * 即：Generator<T>，public T next();中的的T都要替换成传入的String类型。
 */
public class FruitGenerator implements Generator<String> {

    private String[] fruits = new String[]{"Apple", "Banana", "Pear"};

    @Override
    public String next() {
        Random rand = new Random();
        return fruits[rand.nextInt(3)];
    }
}

```

#### 3、泛型方法
泛型方法使得该方法能够独立于类而产生变化。以下是一个基本的指导原则：无论何时，只要你能做到，你就应该尽量使用泛型方法。也就是说，如果使用泛型方法可以取代整个类泛型化，那么就应该只使用泛型方法

定义泛型方法，只需要将泛型参数列表置于返回值之前，如下：

```
public class GenericMethods{
    //普通泛型方法
    public <T> void f(T x){
        Log.d("泛型测试：", x.getClass.getName());
    }
    
    /*
    *泛型方法与可变参数
    *makeList()方法展示了与标准类库中java.util.Arrays.asList()方法相同的功能
    */
    public static <T> List<T> makeList(T.. args){
        List<T> result = new ArrayList<T>();
        for(T item : args){
            result.add(item);
        }
        return result;
    }
    
    public static void main(String[] args){
        GenericMethods gm = new GenericMethods();
        gm.f("");
        gm.f(1);
        gm.f(1.0);
        gm.f(1.0F);
        gm.f('c');
        gm.f(gm);
        
        List<String> ls = makeList("A");
        System.out.println(ls);
        ls = makeList("A", "B", "C");
        System.out.println(ls);
        ls = makeList("ABCDEFG".split(""));
        System.out.println(ls);
    }
}

outout:
泛型测试：java.lang.String
泛型测试：java.lang.Integer
泛型测试：java.lang.Double
泛型测试：java.lang.Float
泛型测试：java.lang.Character
泛型测试：GenericMethods

[A]
[A, B, C]
[, A, B, C, D, E, F, G]
```
注意当使用泛型类时，必须在创建对象的时候指定类型参数的值，而使用泛型方法的时候，通常不必致命参数类型，因为编译器会为我们找出具体的类型。这称为类型参数推断。

### 三、泛型通配符
我们知道Ingeter是Number的一个子类，同时在特性章节中我们也验证过Generic<Ingeter>与Generic<Number>实际上是相同的一种基本类型。那么问题来了，在使用Generic<Number>作为形参的方法中，能否使用Generic<Ingeter>的实例传入呢？在逻辑上类似于Generic<Number>和Generic<Ingeter>是否可以看成具有父子关系的泛型类型呢？

为了弄清楚这个问题，我们使用Generic<T>这个泛型类继续看下面的例子：

```
public void showKeyValue1(Generic<Number> obj){
    Log.d("泛型测试","key value is " + obj.getKey());
}
```

```
Generic<Integer> gInteger = new Generic<Integer>(123);
Generic<Number> gNumber = new Generic<Number>(456);

showKeyValue(gNumber);

// showKeyValue这个方法编译器会为我们报错：Generic<java.lang.Integer> 
// cannot be applied to Generic<java.lang.Number>
// showKeyValue(gInteger);
```

通过提示信息我们可以看到Generic<Integer>不能被看作为`Generic<Number>的子类。由此可以看出:**同一种泛型可以对应多个版本（因为参数类型是不确定的），不同版本的泛型类实例是不兼容的。**

回到上面的例子，如何解决上面的问题？总不能为了定义一个新的方法来处理Generic<Integer>类型的类，这显然与java中的多台理念相违背。因此我们需要一个在逻辑上可以表示同时是Generic<Integer>和Generic<Number>父类的引用类型。由此类型通配符应运而生。

修改方法如下：

```
public void showKeyValue1(Generic<?> obj){
    Log.d("泛型测试","key value is " + obj.getKey());
}
```
类型通配符一般是使用？代替具体的类型实参，注意了，此处’？’是类型实参，而不是类型形参。再直白点的意思就是，此处的？和Number、String、Integer一样都是一种实际的类型，可以把？看成所有类型的父类。是一种真实的类型。

可以解决当具体类型不确定的时候，这个通配符就是 **？**；当操作类型时，不需要使用类型的具体功能时，只使用Object类中的功能。那么可以用 **?** 通配符来表未知类型。

### 四、擦除的神秘之处


> Java泛型是使用擦除来实现的，这意味着当你在使用泛型时，任何具体的类型信息都被擦除了，你唯一知道的就是你在使用一个对象。因此 List<String> 和 List<Integer> 在运行时事实上是相同的类型。这两种形式都被擦除成它们的“原生类型，即 List。


```
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();

Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();

if(classStringArrayList.equals(classIntegerArrayList)){
    Log.d("泛型测试","类型相同");
}

output
泛型测试：类型相同
```
由上也可知擦除的代价是显著的。泛型不能用于显示地引用运行时类型的操作之中，例如转型**instanceof** 操作和 **new** 表达式。因为所有关于参数的类型信息都丢失了，无论何时，当你在编写泛型代码时，必须时刻提醒自己，你只是***看起来好像拥有有关参数的类型信息***而已。

### 五、泛型边界

边界使得你可以在用于泛型的参数类型上设置限制条件。尽管这使得你可以强制规定泛型可以应用的类型，但是其潜在的一个更重要的效果是你可以按照自己的边界类型来调用方法。（擦除移除了类型信息，无界泛型参数只能调用 **Object**调用的方法）

```
/**
 * 由于有了擦除，Java编译器无法将manipulate()必须能够在obj上调用f()这一需求映射到HasF拥有f()这一事实上。
 * @param <T>
 */
class Manipulator<T>{
    private T obj;
    public Manipulator(T x){
        obj = x;
    }

    // Error: Cannot resolve method 'f()'
    /*public void manipulate(){
        obj.f();
    }*/
}

/**
 * 为了调用f()，我们必须协助泛型类，给定泛型类的边界，以此告知编译器只能接受遵循这个边界的类型。由于有了边界，下面的代码就可以编译了。
 * @param <T>
 */
class Manipulator2<T extends HasF>{
    private T obj;
    public Manipulator2(T x){ 
        obj = x; 
    }
    public void manipulate(){
        obj.f();
        
    }
}

public class Manipulation {
    public static void main(String[] args){
        HasF hf = new HasF();
        Manipulator<HasF> manipulator = new Manipulator<>(hf);
        //manipulator.manipulate();
        Manipulator2<HasF> manipulator2 = new Manipulator2<>(hf);
        manipulator2.manipulate();
    }
}

```

### 六、泛型总结

> 泛型正如其名称所暗示的：它是一种方法，通过它可以编写出更“泛化”的代码，这些代码对于它们能够作用的类型有更少的限制，因此单个的代码段能够应用到更多的类型上。



