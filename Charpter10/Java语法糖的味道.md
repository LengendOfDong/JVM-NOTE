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
- 在不遇到算术运算的情况下不会自动拆箱，
