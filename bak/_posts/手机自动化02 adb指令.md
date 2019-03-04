title: 手机自动化02 adb指令
date: 2018-09-28 20:09:46
tags: [adb,手机自动化,自动化测试]
categories: [测试]
---
#### adb与appium
adb 的运行原理是 PC 端的 adb server 与手机端的守护进程 adbd 建立连接，然后 PC 端的 adb client 通过 adb server 转发命令，adbd 接收命令后解析运行。

#### 常用指令
- 查看当前已连接设备
```
adb devices
------------
List of devices attached
CQQCJJFMTWJV8HW4        device
emulator-5554   device
```

<!--more-->


- 使用`-s`获取特定设备信息（多设备时使用）
```
# 当只有一台设备的时候我们可以直接使用adb对设备进行操作
# 多个设备的时候就需要通过-s进行设备的指定
adb -s emulator-5554 shell wm size
```

- 进入adb shell命令行
```
C:\Users\gengzongyuan>adb shell
root@x86:/ # exit

C:\Users\gengzongyuan>
```
- 获取当前手机安装的应用
```
adb shell pm list packages    # 所有应用
adb shell pm list packages -3 # 第三方应用
```

- 安装应用
```
C:\Users\gengzongyuan>adb install -r -g C:\Users\gengzongyuan\Desktop\mobileqq_android.apk
C:\Users\gengzongyuan\Desktop\mobileqq.... 2.9 MB/s (54702331 bytes n 18.151s)
        pkg: /data/local/tmp/mobileqq_android.apk
Success


-r	允许覆盖安装
-s	将应用安装到 sdcard
-d	允许降级覆盖安装
-g	授予所有运行时权限
```
- 卸载应用
```
adb uninstall [包名]
```
- 清除应用数据和缓存
```
adb shell pm clear [包名]
```
- 启动应用程序
```
adb shell am start 包名/启动页名称
adb shell am start com.tencent.mobileqq/.activity.SplashActivity
```
- 强制停止应用程序
```
adb shell am force-stop 包名
adb shell am force-stop com.tencent.mobileqq
```
- 复制文件到设备中
```
adb push <电脑上的文件路径> <设备里的目录>
adb push C:\Users\gengzongyuan\Desktop\mobileqq_android.apk /sdcard/testDir/
C:\Users\gengzongyuan\Desktop\mobileqq.... 2.8 MB/s (54702331 bytes in 18.661s)
```
 - 模拟按键/输入
```
adb shell input keyevent <keycode>
3	HOME 键
4	返回键
····
可以参考github  https://github.com/mzlogin/awesome-adb
```
- 滑动操作
```
adb shell input swipe 起始点x坐标 起始点y坐标 结束点x坐标 结束点y坐标
```
- 输入文本
```
在焦点处于某文本框时，可以通过 input 命令来输入文本。
adb shell input text hello
```
- 截图
```
adb exec-out screencap -p > C:/sc.png
```
- 发送随机事件
#### 参考
https://github.com/mzlogin/awesome-adb