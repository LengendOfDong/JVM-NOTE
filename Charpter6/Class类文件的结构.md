# Class类文件的结构
&emsp;&emsp;Class文件是一组以8位字节为基础单位的二进制流，各个数据项目严格按照顺序紧凑地排列在Class文件之中，中间没有添加任何分隔符，这使得整个Class
文件中存储的内容几乎全部是程序运行的必要数据，没有空隙存在。

&emsp;&emsp;当遇到需要占用8位以上空间的数据项时，则会按照高位在前的方式分割成若干个8位字节进行存储。

&emsp;&emsp;Class文件格式采用一种类似于C语言结构体的伪结构来存储数据，这种伪结构只有两种数据类型：无符号数和表。

&emsp;&emsp;无符号数属于基本的数据类型，无符号数可以用来描述数字、索引引用、数量值或者按照UTF-8编码构成字符串值。

&emsp;&emsp;表示由多个无符号数或者其他表作为数据项构成的复合数据类型，所有表都习惯以“_info”结尾。表用于描述有层次关系的复合结构的数据。

## 魔数与Class文件的版本
&emsp;&emsp;每个Class文件的头4个字节称为魔数，它的唯一作用是确定这个文件是否为一个能被虚拟机接受的Class文件。Class文件的魔数值为OxCAFEBABE。紧接着
魔数的4个字节存储的是Class文件的版本号，第5和第6个字节是次版本号，第7和第8个字节是主版本号。

&emsp;&emsp;Java的版本号是从45开始的，JDK1.1之后的每个JDK大版本发布主版本号向上加1。

## 常量池
&emsp;&emsp;紧接着主次版本号之后的是常量池入口，常量池可以理解成Class文件中的资源仓库，它是Class文件结构中与其他项目关联最多的数据类型，也是占用Class
文件空间最大的数据项目之一，同时它还是在Class文件中第一个出现的表类型数据项目。

&emsp;&emsp;Class文件结构中只有常量池的容量计数是从1开始，对于其他集合类型，包括索引集合、字段表集合、方法表集合等的容量计数都与一般习惯相同，是从0开
始的。

&emsp;&emsp;常量池中主要存放两大类常量：字面量和符号引用。字面量比较接近Java语言层面的常量概念，如文本字符串、声明为final的常量值等，而符号引用则属于
编译原理方面的概念，包括下面三类常量：
- 类和接口的全限定名
- 字段的名称和描述符
- 方法的名称和描述符

## 访问标志
&emsp;&emsp;在常量池结束之后，紧接着的两个字节代表访问标志，这个标志用于识别一些类或者接口层次的访问信息，包括这个Class是类还是接口；是否定义为public类型，是否定义为abstract类型。

## 类索引、父类索引与接口索引集合
&emsp;&emsp;类索引和父类索引都是一个u2类型的数据，而接口索引集合是一组u2类型的数据的集合，Class文件中的这三项数据来确定这个类的继承关系。

&emsp;&emsp;类索引用于确定这个类的全限定名，父类索引用于确定这个类的父类的全限定名，接口索引集合就用来描述这个类实现了哪些接口，这些被实现的接口将
按implements语句后的接口顺序从左到右排列在接口索引集合中。

## 字段表集合
&emsp;&emsp;字段表用于描述接口或者类中声明的集合。字段包括类级变量以及实例级变量，但不包括在方法内部声明的局部变量。

&emsp;&emsp;描述符的作用是用来描述字段的数据类型、方法的参数列表（包括数量、类型和顺序）和返回值。

&emsp;&emsp;根据描述符规则，基本数据类型以及代表无返回值的void类型都用一个大写字符来表示。

|标识字符|含义|标识字符|含义|
|--|--|--|--|
|B|基本类型byte|J|基本类型long|
|C|基本类型char|S|基本类型short|
|D|基本类型double|Z|基本类型boolean|
|F|基本类型float|V|特殊类型void|
|I|基本类型int|L|对象类型如Ljava/lang/Object|

&emsp;&emsp;对于数据类型，每一维度将使用一个前置的“["字符来描述，如一个定义为”java.lang.String[][]“类型的二维数组，将被记录为：”[[Ljava/lang/String;“,一个整形数组”int[]“将被记录为”[I“。

&emsp;&emsp;用描述符来描述方法时，按照先参数列表，后返回值的顺序描述，参数列表按照参数的严格顺序放在”（）“之内。

&emsp;&emsp;方法void inc()的描述符为”()V“ , 方法java.lang.String toString()的描述符为”()Ljava/lang/String;“



