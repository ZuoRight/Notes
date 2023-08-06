# Jmeter

[官网介绍](https://jmeter.apache.org){ .md-button .md-button--primary }

![20211124153427](http://image.zuoright.com/20211124153427.png)

解压后可以命令行切换到`apache-jmeter-5.4.1/bin`路径下，使用`sh jmeter`命令启动，但为了方便，可以配置环境变量在任意路径下打开，以下是Mac系统的环境变量配置

```shell
# Java
# Jmeter
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_311.jdk/Contents/Home
export JMETER_HOME=/Users/7c/apache-jmeter-5.4.1
export PATH=$PATH:$JAVA_HOME/bin:$PATH:.:$JMETER_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JMETER_HOME/lib/ext/ApacheJMeter_core.jar:$JMETER_HOME/lib/jorphan.jar
```

![20211124154556](http://image.zuoright.com/20211124154556.png)

> 更改默认语言为中文，编辑`apache-jmeter-5.4.1/bin/jmeter.properties`文件，将`#language=en`改为`language=zh_CN`即可

!!! Tips
    如果启动后有闪退或者报错等问题，大概率跟jdk版本有关，我在`jdk-11.0.11`及以上版本都有遇到过各种问题，然后降低到`jdk1.8.0_311.jdk`版本后就稳定多了，哎。
