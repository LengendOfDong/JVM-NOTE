# Java语法糖的味道
&emsp;&emsp;语法糖是把双刃剑，一方面会方便程序员进行开发，提高效率，或者提升语法的严谨性，或者减少编码出错。另一方面大量的语法糖会让程序员产生依赖，而
忽略了程序代码的真实面目。

## 泛型与类型擦除  
&emsp;&emsp;泛型本质是参数化类型的应用，这种参数类型可以用在类、接口、方法的创建中，分别称为泛型类、泛型接口和泛型方法。

&emsp;&emsp;泛型技术在C#中也有应用，不过和Java大为不同。C#里面是切实存在的，List<int>和List<String>就是两个不同的类型，它们在系统运行期生成，有自
己的虚方法表和类型数据，这种实现称为类型膨胀，基于这种方法实现的泛型称为真实泛型。

&emsp;&emsp;Java语言中的泛型只在源码中存在，在编译后的字节码文件中，就已经替换为原来的原生类型（Raw Type,也称为裸类型），并且在相应的地方插入了强制
转型代码。

&emsp;&emsp;对于运行期的Java语言来说，ArrayList<int>与ArrayList<String>就是同一个类，泛型技术实际上是Java语言的一颗语法糖，Java语言中的泛型实
现称为类型擦除，基于这种方法实现的泛型称为伪泛型。

## 自动装箱、拆箱和遍历循环

源代码：
```java
public class AutoBox {
    public static void main(String[] args){
        Integer a = 1;
        Integer b = 2;
        Integer c = 3;
        Integer d = 3;
        Integer e = 321;
        Integer f = 321;
        Long g = 3L;

        System.out.println(c == d);
        System.out.println(e == f);
        System.out.println(c == (a + b));
        System.out.println(c.equals(a + b));
        System.out.println(g == (a + b));
        System.out.println(g.equals(a + b));
    }
}
```

反编译之后的代码如下：
```java
public class AutoBox {
  public static void main(String[] args) {
    Integer a = Integer.valueOf(1);
    Integer b = Integer.valueOf(2);
    Integer c = Integer.valueOf(3);
    Integer d = Integer.valueOf(3);
    Integer e = Integer.valueOf(321);
    Integer f = Integer.valueOf(321);
    Long g = Long.valueOf(3L);
    System.out.println((c == d));
    System.out.println((e == f));
    System.out.println((c.intValue() == a.intValue() + b.intValue()));
    System.out.println(c.equals(Integer.valueOf(a.intValue() + b.intValue())));
    System.out.println((g.longValue() == (a.intValue() + b.intValue())));
    System.out.println(g.equals(Integer.valueOf(a.intValue() + b.intValue())));
  }
}
```
最后的结果输出为：
```java
true
false
true
true
true
false
```
&emsp;&emsp;上面例子中有几点需要注意：
- 在不遇到算术运算的情况下不会自动拆箱，所以在上面的例子中只有第3，4，5，6条才有拆箱操作
- equals方法不处理数据转型的关系，所以最后一条左边是Long型，右边是Integer类型
- java语言对Integer的缓存做了约束，规定其范围为：（-128-127），在这个范围内取值，会从缓存中取，超过这个范围会重新new一个对象出来

&emsp;&emsp;在上面几条规则下，判断就很容易：
- 第一条“==”判断对象是否是同一个对象，在（-128-127）这个范围内，指向同一个对象，所以为true
- 第二条“==”判断对象是否是同一个对象，在（-128-127）这个范围外，重新new一个对象，所以为false
- 第三条先进行算术运算，会发生自动拆箱动作，“==”判断两边值是否相等，所以为true
- 第四条Integer的equals方法重载了Object对象的equals方法，用来判断值是否相等，所以为true
- 第五条依然是先进行算术运算，会发生自动拆箱动作，“==”判断两边拆箱后的值是否相等，所以为true
- 第六条先进行算术运算，由于equals方法参数需要为对象类型，所以又进行自动装箱动作，接着判断两边的对象值是否相等，由于equals方法不处理数据转型的关系
，所以为false

## 条件编译
&emsp;&emsp;Java语言中条件编译的实现，也是Java语言的一颗语法糖，根据布尔常量值的真假，编译器将会把分支中不成立的代码块消除掉，这一工作将在编译器解除语法糖阶段完成。

&emsp;&emsp;Java语言除了泛型、自动装箱、自动拆箱、遍历循环、变长参数和条件编译之外，还有其他语法糖，如内部类、枚举类、断言语句、对枚举和字符串的switch支持、try语句中定义和关闭资源

