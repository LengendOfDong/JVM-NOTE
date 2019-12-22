# Tomcat：正统的类加载器架构
&emsp;&emsp;功能健全的Web服务器，要解决如下的几个问题：
- 部署在同一个服务器上的两个web服务应用程序所使用的Java类库可以实现互相隔离。
- 部署在同一个服务器上的两个web服务应用程序所使用的Java类库可以共享。类库在使用时都要被加载到服务器内存，如果类库不能共享，虚拟机的方法区就会很容易出现
过度膨胀的风险。
- 服务器需要尽可能地保证自身的安全不受部署的Web应用程序影响。目前许多Java Web服务器自身也是使用Java语言来实现的，所以出于安全考虑，web服务器自身使用的
类库应该与应用程序的类库互相独立。
- 支持JSP应用的Web服务器，大多数都需要支持HotSwap功能。JSP文件由于其纯文本存储的特性，运行时修改的概率远远大于第三方类库或程序自身的Class文件。“主流”
的Web服务器都会支持JSP生成类的热替换。

&emsp;&emsp;各种Web服务器提供好几个ClassPath路径供用户存放第三方类，这些路径一般都以“lib”或“classes”命名。被放置到不同路径中的类库，具备不同的访问
范围和服务对象。

&emsp;&emsp;在Tomcat目录结构中，有3组目录（“/common/*”,"/server/*","/shared/*"）可以存放目录，另外还可以加上WEB服务器自身的目录“/WEB-INF/*”。
- 放置在/common目录中：类库可被tomcat和所有的Web应用程序共同使用。
- 放置在/server目录中：类库可被Tomcat使用，对所有的Web应用程序都不可见。
- 放置在/shared目录中：类库可被所有的Web应用程序共同使用，但对Tomcat自己不可见。
- 放置在/WebApp/WEB-INF目录中：类库仅仅可以被此Web应用程序使用，对Tomcat和其他Web应用程序都不可见。

&emsp;&emsp;Tomcat为了支持这套目录结构，并对目录里面的类库进行加载和隔离，自定义的多个类加载器，按照经典的双亲委派模型来实现。

&emsp;&emsp;从Tomcat的存放目录可以分析出：
- 首先会有一个通用的自定义类加载器用来加载/common目录
- 接着/server与/shared目录中，存在互斥的情况，所以应该是互斥的两个分支，分别有两个类加载器来分别加载/server目录和/shared目录。
- 然后根据/WEB-INF/目录中存放文件的访问情况，应该是接在/shared目录类加载器下，因为都对Tomcat不可见，而/WEB-INF/中存放只是部分Web应用程序不可见。
- 最后/WEB-INF/目录下要存放JSP文件，所以还需要一个JSP类加载器。

&emsp;&emsp;WebApp类加载器和Jsp类加载器通常会存在多个实例，每一个Web应用程序对应一个WebApp类加载器，每一个JSP文件对应一个Jsp类加载器。

&emsp;&emsp;CommonClassLoader能加载的类都可以被CatalinaClassLoader和SharedClassLoader使用，而CatalinaClassLoader和SharedClassLoader自
己能加载的类则与对方相互隔离。WebAppClassLoader可以使用SharedClassLoader加载到的类，但各个WebAppClassLoader实例之间相互隔离。

&emsp;&emsp;JasperLoader的加载范围仅仅是这个JSP文件所编译出来的那一个Class,它出现的目的就是为了被丢弃：当服务器检测到JSP文件被修改时，会替换掉目前
的JasperLoader的实例，并通过再建立一个新的Jsp类加载器来实现JSP文件的HotSwap功能。

&emsp;&emsp;Tomcat 6.x中tomcat/conf/catalina.properties配置文件中没有配置server.loader和share.loader，所以把/common、/server和/shared
三个目录默认合并到一起变成一个/lib目录。
