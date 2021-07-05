## Java类加载器有哪些

jdk 自带有三个类加载器: Bootstrap ClassLoader、ExtClassLoader、AppClassLoader。

## Bootstrap

ExtClassLoader 的父类加载器、负责默认加载 JAVA_HOME/lib 下的 jar 包和 class 文件。

## ExtClassLoader 

AppClassLoader 的父类加载器、负责加字啊 JAVA_HOME/lib/ext 下的 jar 包和 class 文件。

## AppClassLoader

自定义加载器的父类，加载 classpath 下的类文件。系统类加载器和线程上下文加载器。

