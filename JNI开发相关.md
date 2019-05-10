JNI开发相关

首先要在java文件中定义好一个native的方法, 才能有接下来的操作

将javac编译出来的class文件转换成.h头文件, 在jdk9以上, javah功能集成进了java中, 使用java -h命令即可

jkd8: javah -jni -cp out\production\JniDemo\ -d jni\ com.jayson.jni.Main
jdk11: javac -h . HelloJni.java

将.c文件编译成.o文件(中间文件)命令如下:
gcc -c -I"D:\DevEnvironment\Java\jdk1.8.0_192\include" -I"D:\DevEnvironment\Java\jdk1.8.0_192\include\win32" Hello.c

将.o文件编译成.so或是.dll文件命令如下:
gcc -shared -o hello.dll Hello.o

如果图省事的话可以用下面的命令一次性直接输出动态库
gcc -I"D:\DevEnvironment\Java\jdk1.8.0_192\include" -I"D:\DevEnvironment\Java\jdk1.8.0_192\include\win32" -shared -o hello1.dll Hello.c

在实际操作的过程中遇到了这样一个问题, 我切换了JAVA_HOME中jdk的版本, 然后原本的dll就不可用了, 网上都说需要改环境才可以继续, 但是经过排查发现只要将生成的动态库改成与环境一致(jdk位数)即可, 
由于使用的是MinGW64位版本, 可以交叉编译(支持在32位系统上生成64位的库, 64位系统上生成32位的库), 所以只要通过参数控制动态库的版本即可
命令如下
gcc -m64 -I"D:\DevEnvironment\Java\jdk_10\include" -I"D:\DevEnvironment\Java\jdk_10\include\win32" -shared -o jni\Jayson.dll jni\JaysonJni.c


在运行java程序是会报错java.lang.UnsatisfiedLinkError, 这是没有配置运行时的lib库路径
即找不到我们生成的dll文件。因为在Windows中JVM的java.library.path属性即为环境变量Path指定的目录，而我们生成的dll并未放入到Path指定的任何一个目录中，因此我们需要告诉JVM，dll文件在哪个目录中。点击Run > Edit Configurations.
在VM options中加入java.library.path，指定dll（或so）文件所在的目录，比如本文中dll放在项目目录中的lib中，如下：

-Djava.library.path=D:\GithubRepo\JniDemo\jni







参考链接:
由.o文件生成.a文件(静态库)或者.dll文件(动态库)
https://blog.csdn.net/zhuzeji/article/details/41383263

Java调用动态库方法说明-最详细
https://www.cnblogs.com/langtianya/p/3470896.html

java native方法及JNI实例
https://www.cnblogs.com/haitaofeiyang/p/7696013.html

Android.mk详解
https://blog.csdn.net/wzy_1988/article/details/39958727

GCC相关知识
http://c.biancheng.net/view/700.html

