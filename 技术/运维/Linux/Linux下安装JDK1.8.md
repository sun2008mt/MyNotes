1. Oracle官网下载jdk1.8，选择版本时根据电脑硬件和操作系统选择（Linux，x64）；

2. 下载好tar.gz压缩文件后解压缩到安装目录；

3. 修改全局环境变量，编辑文件/etc/profile，在文件末尾添加以下内容：
```
export JAVA_HOME=/opt/jdk1.8.0_162

export JRE_HOME=${JAVA_HOME}/jre

export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH

export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin

export PATH=$PATH:${JAVA_PATH}
```
4. 通过source命令使环境变量配置立即生效
```
source /etc/profile
```
5. 通过java和javac命令测试是否安装成功
```
java
javac
java -version
```

# 参考：

1. https://www.cnblogs.com/xuliangxing/p/7066913.html