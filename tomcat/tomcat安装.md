# tomcat 安装

## 1.下载

[下载页面](https://tomcat.apache.org/whichversion.html)

```bash

wget -c https://dlcdn.apache.org/tomcat/tomcat-11/v11.0.4/bin/apache-tomcat-11.0.4.zip
```

## 2.解压

```bash

unzip apache-tomcat-11.0.4.zip
```

## 3.启动

```bash

# ll /home/hello/local/tomcat/apache-tomcat-11.0.4/bin

-rw-r----- 1 hello hello  31K Feb 13 10:17 bootstrap.jar
-rw-r----- 1 hello hello  14K Feb 13 10:17 catalina.bat
-rwxr-x--- 1 hello hello  21K Feb 13 10:17 catalina.sh
-rw-r----- 1 hello hello 1.7K Feb 13 10:17 catalina-tasks.xml
-rw-r----- 1 hello hello 2.1K Feb 13 10:17 ciphers.bat
-rwxr-x--- 1 hello hello 2.0K Feb 13 10:17 ciphers.sh
-rw-r----- 1 hello hello  27K Feb 13 10:17 commons-daemon.jar
-rw-r----- 1 hello hello 211K Feb 13 10:17 commons-daemon-native.tar.gz
-rw-r----- 1 hello hello 2.0K Feb 13 10:17 configtest.bat
-rwxr-x--- 1 hello hello 1.9K Feb 13 10:17 configtest.sh
-rwxr-x--- 1 hello hello 8.2K Feb 13 10:17 daemon.sh
-rw-r----- 1 hello hello 2.1K Feb 13 10:17 digest.bat
-rwxr-x--- 1 hello hello 2.0K Feb 13 10:17 digest.sh
-rw-r----- 1 hello hello 3.6K Feb 13 10:17 makebase.bat
-rwxr-x--- 1 hello hello 3.4K Feb 13 10:17 makebase.sh
-rw-r----- 1 hello hello 2.1K Feb 13 10:17 migrate.bat
-rwxr-x--- 1 hello hello 2.0K Feb 13 10:17 migrate.sh
-rw-r----- 1 hello hello 3.4K Feb 13 10:17 setclasspath.bat
-rwxr-x--- 1 hello hello 3.9K Feb 13 10:17 setclasspath.sh
-rw-r----- 1 hello hello 2.0K Feb 13 10:17 shutdown.bat
-rwxr-x--- 1 hello hello 1.9K Feb 13 10:17 shutdown.sh
-rw-r----- 1 hello hello 2.0K Feb 13 10:17 startup.bat
-rwxr-x--- 1 hello hello 1.9K Feb 13 10:17 startup.sh
-rw-r----- 1 hello hello  50K Feb 13 10:17 tomcat-juli.jar
-rw-r----- 1 hello hello 461K Feb 13 10:17 tomcat-native.tar.gz
-rw-r----- 1 hello hello 3.6K Feb 13 10:17 tool-wrapper.bat
-rwxr-x--- 1 hello hello 4.5K Feb 13 10:17 tool-wrapper.sh
-rw-r----- 1 hello hello 2.0K Feb 13 10:17 version.bat
-rwxr-x--- 1 hello hello 1.9K Feb 13 10:17 version.sh

# 启动
./shutdown.sh
# 停止
./startup.sh
```

