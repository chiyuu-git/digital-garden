---
{"dg-publish":true,"permalink":"/programming/hybrid/android/"}
---


# Android 是什么

安卓是一种基于 Linux 的开源的操作系统

主要适用于智能设备，如智能手机、平板电脑和智能电视等

# 安卓 Adb 调试

你用 adb logcat | grep "RenderImpl" 查看你调用客户端的指令 dev 版

SwanAppNaViewModule_ 可以查看 dispatchCommand 的

```
# 看错误 / 崩溃日志，找到 E AndroidRuntime: FATAL EXCEPTION
adb logcat *:E

zsh interprets the * as that you want to expand to files ending in :E. Wrap it inside single quotes or put noglob before adb:
adb logcat '*:E' or noglob adb logcat *:E
```

```
# 看 console 日志
adb logcat | grep V8Console
```

​ 安卓开发过程中经常遇到真机崩溃的情况，这时如何拿到崩溃日志是我们十分关心的问题。手机崩溃后，不要再进行别的操作，保留现场。连接手机和电脑，使用 adb 命令抓取崩溃日志。

```cpp
adb logcat *:W
```

注意：这句命令的意思是显示所有优先级大于等于警告（Warning）的日志，查找崩溃问题一般用：

```html
adb logcat *:E
```

注意：这句命令的意思是显示所有优先级大于等于错误（Error）的日志。

日志的优先级一般有如下几档，由低到高：

1、VERBOSE，一般信息，使用 Log.v 输出颜色为黑色且日志级别大于等于 VERBOSE 的信息；

2、DEBUG，调试信息，使用 Log.d 输出颜色为蓝色且日志级别大于等于 DEBUG 的信息；

3、INFO，标识信息，使用 Log.i 输出绿色且日志级别大于等于 INFO 的信息；

4、WARN，警告信息，使用 Log.w 输出橙色且日志级别大于等于 WARN 的信息；

5、ERROR，错误信息，使用 Log.e 输出红色且日志级别大于等于 ERROR 的信息。

## 常用命令

adb kill-server

adb start-server

adb tcpip 8888

adb connect 172.18.226.25:8888

adb shell am start -n com.baidu.searchbox/com.baidu.swan.apps.impl.debug.swancard.SwanCardDebugActivity

## 写入日志

adb logcat '*:E' > /Users/pangjing02/1.txt

adb logcat -t '03-19 11:30:00.820' '*:E' > /Users/pangjing02/1.txt

https://blog.csdn.net/x30465947/article/details/79499269

https://developer.android.com/studio/command-line/logcat?hl=zh-cn

# 真机节点调试

![ddcf5c56635b785e0587d3bc9fcf41b8](/img/user/programming/hybrid/android/ddcf5c56635b785e0587d3bc9fcf41b8.png)

![30e17e67623298f49766b6279a4e7f9b](/img/user/programming/hybrid/android/30e17e67623298f49766b6279a4e7f9b.png)

![c635cc44682ab6a7878d373a9f1bd577](/img/user/programming/hybrid/android/c635cc44682ab6a7878d373a9f1bd577.png)

## Gradle 编译配置

https://ku.baidu-int.com/knowledge/HFVrC7hq1Q/xjYiDpu--c/CM1f0ObQIk/f-QVDT_xuPLbwi
