# OSGI:灵活的类加载器架构
&emsp;&emsp;OSGI(Open Service Gateway Initiative)是OSGI联盟制定的一个基于Java语言的动态模块化规范。

&emsp;&emsp;OSGI目的是使服务提供商通过住宅网关为各种家用智能设备提供各种服务，后来这个规范成为Java“事实上”的模块化标准。OSGI在Java程序员中最著名的
应用案例就是Eclipse IDE,另外还有许多大型的软件平台和中间件服务器都基于或声明。

&emsp;&emsp;OSGI的每个模块称为Bundle，可以声明它所依赖的Java Package（通过Import-Package描述），也可以声明它允许导出发布的Java Package（通
过Export-Package描述）。

&emsp;&emsp;OSGI的程序很可能可以实现模块级的热插拔功能，当程序升级更新或者调试除错时，可以只停用、重新安装然后启用程序的其中一部分，这对企业级程序开发
来说是一个非常有诱惑力的特性。

&emsp;&emsp;OSGI之所以能有上述“诱人”的特点，要归功于它灵活的类加载器架构。不涉及具体的Package时，各个Bundle加载器都是平级关系，只有具体使用某个
Package和Class的时候，才会根据Package导入导出定义来构造Bundle间的委派和依赖。

&emsp;&emsp;一个Bundle类加载器为其他Bundle提供服务时，会根据Export-Package列表严格控制访问范围。如果一个类存在于Bundle的类库中但是没有被Export，
那么这个Bundle的类加载器能够找到这个类，但是不会提供给其他Bundle使用，而且OSGI平台也不会把其他Bundle的类加载请求分配给这个Bundle来处理。

&emsp;&emsp;在OSGI中，类加载时可能进行的查找规则如下：
- 以java.*开头的类，委派给父类加载器加载
- 否则，委派列表名单内的类，委派给父类加载器加载
- 否则，Import列表中的类，委派给Export这个类的Bundle的类加载器加载
- 否则，查找当前Bundle的ClassPath,使用自己的类加载器加载
- 否则，查找是否在自己的Fragment Bundle中，如果是，则委派给Fragment Bundle的类加载器加载
- 否则，查找Dynamic Import列表的Bundle，委派给对应Bundle的类加载器加载。
- 否则，类查找失败

&emsp;&emsp;并非所有的应用都适合采用OSGI作为基础架构，OSGI在提供强大功能的同时，也引入了额外的复杂度，带来了线程死锁和内存泄露的风险。

