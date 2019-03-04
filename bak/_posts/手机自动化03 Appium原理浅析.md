title: 手机自动化03 Appium原理浅析
date: 2018-09-29 19:03:12
tags: [appium,手机自动化]
categories: [测试]
---
#### 启动你的程序吧
##### 启动appium server
- 启动appium程序
![](https://upload-images.jianshu.io/upload_images/2572206-fa75bd5c75f603ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- start server即可
![](https://upload-images.jianshu.io/upload_images/2572206-a671e33afc8ffc21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### 在设备上安装应用程序
- 下载下来并且拖动到设备中进行安装
`链接：https://pan.baidu.com/s/10_69_v6arAdSrEIhdPJTFw 密码：cn3m`

- 如果不进行拖动的话，也可以使用上一章节我们学习的adb指令进行安装
```
adb install -r -g C:\Users\gengzongyuan\Desktop\mobileqq_android.apk
```

<!--more-->

##### 配置解析
- 配置信息
```
desc = {
    'platformName':'Android',   # 设备类型
    'platformVersion':'5.1.1',  # 安卓版本
    'deviceName':'emulator-5554',   # 设备名称
    'noReset':True, # 是否不重置应用，默认为false（重置应用）
    'unicodeKeyboard': True,  # 由于appium可以使用自己的输入法来解决一些中文输入的问题，所以我们一般默认使用appium的输入法
    'resetKeyboard':True,# resetKeyboard如果设置会true，会将输入法重置为运行程序前的输入法

    'appPackage':'com.tencent.mobileqq',    # 首次进入程序调用的安装包
    'appActivity':'.activity.SplashActivity',  # 调用安装包之后进入的启动页
}
```


- 打开app,进入页面
- windows调用`adb logcat |findstr  START` mac调用`adb logcat |grep START`
![](https://upload-images.jianshu.io/upload_images/2572206-20f80e38b7c9411c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 这里的`com.tencent.mobileqq/.activity.LoginActivity`就是`包名/当前页面名称`

##### 启动程序
```
from appium import webdriver
import time

desc = {
    'platformName':'Android',   # 设备类型
    'platformVersion':'5.1.1',  # 安卓版本
    'deviceName':'emulator-5554',   # 设备名称
    'noReset':True, # 是否不重置应用，默认为false（重置应用）
    'unicodeKeyboard': True,  # 由于appium可以使用自己的输入法来解决一些中文输入的问题，所以我们一般默认使用appium的输入法
    'resetKeyboard':True,# resetKeyboard如果设置会true，会将输入法重置为运行程序前的输入法

    'appPackage':'com.tencent.mobileqq',    # 首次进入程序调用的安装包
    'appActivity':'.activity.SplashActivity',  # 调用安装包之后进入的启动页
}

# 启动服务
driver = webdriver.Remote("http://127.0.0.1:4723/wd/hub",desc)
# 由于启动页面刷新的比较慢，为了避免找不到控件报错，我们等待几秒钟
time.sleep(5)
# 寻找登录按钮并进行点击操作
driver.find_element_by_android_uiautomator('new UiSelector().text("登 录")').click()
# 寻找密码框
loginInput = driver.find_element_by_id("com.tencent.mobileqq:id/password")
print(loginInput)
# 在密码框中输入内容
driver.set_value(loginInput,"password")
```

#### 日志解析
```
[Appium] Welcome to Appium v1.6.5
[Appium] Appium REST http interface listener started on 0.0.0.0:4723

-------------
启动appium server，并持续监听所有IP发送到4723端口上的消息
--------------


[HTTP] --> POST /wd/hub/session {"capabilities":{"alwaysMatch":{"appium:noReset":true,"appium:platformVersion":"5.1.1","appium:appPackage":"com.tencent.mobileqq","appium:appActivity":".activity.SplashActivity","platformName":"Android","appium:deviceName":"emulator-5554"},"firstMatch":[{}]},"desiredCapabilities":{"appPackage":"com.tencent.mobileqq","platformName":"Android","appActivity":".activity.SplashActivity","noReset":true,"deviceName":"emulator-5554","platformVersion":"5.1.1"}}
[MJSONWP] Calling AppiumDriver.createSession() with args: [{"appPackage":"com.tencent.mobileqq","platformName":"Android","appActivity":".activity.SplashActivity","noReset":true,"deviceName":"emulator-5554","platformVersion":"5.1.1"},null,{"alwaysMatch":{"appium:noReset":true,"appium:platformVersion":"5.1.1","appium:appPackage":"com.tencent.mobileqq","appium:appActivity":".activity.SplashActivity","platformName":"Android","appium:deviceName":"emulator-5554"},"firstMatch":[{}]}]
[BaseDriver] Event 'newSessionRequested' logged at 1538204304428 (14:58:24 GMT+0800 (中国标准时间))
-------------
向appium server发送http请求，用于获取对应的session
--------------

[Appium] Creating new AndroidDriver (v1.20.0) session
[Appium] Capabilities:
[Appium]   appPackage: 'com.tencent.mobileqq'
[Appium]   platformName: 'Android'
[Appium]   appActivity: '.activity.SplashActivity'
[Appium]   noReset: true
[Appium]   deviceName: 'emulator-5554'
[Appium]   platformVersion: '5.1.1'
[AndroidDriver] AndroidDriver version: 1.20.0
[BaseDriver] Session created with session id: e6cb349d-d651-4531-aa87-038d3db650f9
[AndroidDriver] Getting Java version
[AndroidDriver] Java version is: 1.8.0_151
-------------
创建一个AndroidDriver,通过驱动程序和安卓设备进行连接
-------------


[ADB] Checking whether adb is present
[ADB] Using adb.exe from D:\study\zidon.exe
[AndroidDriver] Retrieving device list
[ADB] Trying to find a connected android device
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[AndroidDriver] Looking for a device with Android '5.1.1'
[ADB] Setting device id to emulator-5554
[ADB] Getting device platform version
[ADB] Getting connecl","getprop","ro.build.version.release"]
[AndroidDriver] Using device: emulator-5554
[ADB] Checking whether adb is present
[ADB] Using adb.exe from D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe
[ADB] Setting device id to emulator-5554
-------------
进行和设备的初始化连接，通过我们输入的初始值，找到设备，同时我们知道了adb server和adbd进行通信的端口是5037
-------------


[AndroidDriver] App file was not listed, instead we're going to run com.tencent.mobileqq directly on the device
-------------
这句话耐人寻味，“没有找到app file，我们将会直接运行com.tencent.mobileqq程序”
是不是在进行初始化的时候，我们还可以进行一个前置的安装软件包的操作
-------------




[AndroidDriver] Checking whether package is present on the device
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","pm","list","packages","com.tencent.mobileqq"]
-------------
检查是否正确安装了软件包
-------------


[AndroidDriver] Starting Android session
[ADB] Running 'D:\study\zidonghua\appliume\android-s5037,"-s","emulator-5554","wait-for-device"]
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","echo","ping"]
[Logcat] Starting logcat capture
-------------
和服务器进行校验，校验了adb对设备的链接
-------------



[AndroidDriver] Pushing settings apk to device...
[ADB] Getting install status for io.appium.settings
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","pm","list","packages","io.appium.settings"]
[ADB] App is installed
[ADB] Getting package info for io.appium.settings
[ADB] Getting connected devices...
[ADB] Checking whether aapt is present
[ADB] Using aapt.exe from D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\build-tools\28.0.2\aapt.exe
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","dumpsys","package","io.appium.settings"]
[ADB] Cannot read version codes of C:\Users\gengzongyuan\AppData\Local\Programs\appium-desktop\resources\app\node_modules\appium\node_modules\io.appium.settings\app\build\outputs\apk\settings_apk-debug.apk and/or io.appium.settings. Assuming correct app version is already installed
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","getprop","ro.build.version.sdk"]
[ADB] Device API level: 22
[ADB] Gr-5554","shell","dumpsys","package","io.appium.settings"]
[AndroidDriver] Pushing unlock helper app to device...
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","install","C:\\Users\\gengzongyuan\\AppData\\Local\\Programs\\appium-desktop\\resources\\app\\node_modules\\appium\\node_modules\\appium-unlock\\bin\\unlock_apk-debug.apk"]
[ADB] Device API level: 22
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","settings","put","secure","mock_location","1"]
[ADB] Getting device platform version
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","getprop","ro.build.version.release"]
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","wm","size"]
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","getprop","ro.product.model"]
[ADB] Current device property 'ro.product.model': huawei nxt-al10
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","getprop","ro.product.manufacturer"]
[ADB] Current device property 'ro.product.manufacturer': huawei
-------------
安装io.appium.settings和unlock helper两个app
通过日志可以看到笔者已经安装过这两个软件了
-------------



[AndroidDriver] No app sent in, not parsing package/activity
[AndroidDriver] No app capability. Assuming it is already on the device
[AndroidBootstrap] Watching for bootstrap disconnect
[ADB] Forwarding system: 4724 to device: 4724
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","forward","tcp:4724","tcp:4724"]
-------------
监听4727端口，用于给AndroidBootstrap使用
-------------



[UiAutomator] Starting UiAutomator
[UiAutomator] Moving to state 'starting'
[UiAutomator] Parsing uiautomator jar
[UiAutomator] Found jar name: 'AppiumBootstrap.jar'
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","push","C:\\Users\\gengzongyuan\\AppData\\Local\\Programs\\appium-desktop\\resources\\app\\node_modules\\appium\\node_modules\\appium-android-bootstrap\\bootstrap\\bin\\AppiumBootstrap.jar","/data/local/tmp/"]
-------------
将AppiumBootstrap.jar的jar包从本机传输到设备上
-------------


[ADB] Attempting to kill all uiautomator processes
[ADB] Getting all processes with uiautomator
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","ps"]
[ADB] No uiautomator process found to kill, continuing...
-------------
查找当前设备上是否已经执行了AppiumBootstrap进程
如果有就kill掉，在笔者的设备上没有找到
-------------


[UiAutomator] Starting UIAutomator
[ADB] Creating ADB subprocess with args: ["-P",5037,"-s","emulator-5554","shell","uiautomator","runtest","AppiumBootstrap.jar","-c","io.appium.android.bootstrap.Bootstrap","-e","pkg","com.tencent.mobileqq","-e","disableAndroidWatchers",false,"-e","acceptSslCerts",false]
[UiAutomator] Moving to state 'online'
[AndroidBootstrap] Android bootstrap socket is now connected
[ADB] Getting connected devices...
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Client connected
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","dumpsys","window"]
[AndroidDriver] Screen already unlocked, doing nothing
[ADB] Device API level: 22
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
-------------
启动UIAutomator，同时调用adb指令把设备的window信息传递给UIAutomator
-------------

[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","am","start","-W","-n","com.tencent.mobileqq/.activity.SplashActivity","-S"]
-------------
启动app，这里的
-W：等待启动完成
-S:关闭Activity所属的App进程后再启动Activity
-n 指定包名（可以省略）
-------------


[Appium] New AndroidDriver session created successfully, session e6cb349d-d651-4531-aa87-038d3db650f9 added to master session list
[BaseDriver] Event 'newSessionStarted' logged at 1538204316059 (14:58:36 GMT+0800 (中国标准时间))
[MJSONWP] Responding to client with driver.createSession() result: {"platform":"LINUX","webStorageEnabled":false,"takesScreenshot":true,"javascriptEnabled":true,"databaseEnabled":false,"networkConnectionEnabled":true,"locationContextEnabled":false,"warnings":{},"desired":{"appPackage":"com.tencent.mobileqq","platformName":"Android","appActivity":".activity.SplashActivity","noReset":true,"deviceName":"emulator-5554","platformVersion":"5.1.1"},"appPackage":"com.tencent.mobileqq","platformName":"Android","appActivity":".activity.SplashActivity","noReset":true,"deviceName":"emulator-5554","platformVersion":"5.1.1","deviceUDID":"emulator-5554","deviceScreenSize":"1080x1920","deviceModel":"huawei nxt-al10","deviceManufacturer":"huawei"}
[HTTP] <-- POST /wd/hub/session 200 11635 ms - 745 
-------------
返回session信息
到此，我们一开始向appium server发送的请求才最终的返回数据
-------------



[HTTP] --> POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/element {"using":"-android uiautomator","value":"new UiSelector().text(\"登 录\")","sessionId":"e6cb349d-d651-4531-aa87-038d3db650f9"}
-------------
client发送了一条寻找登录控件的http请求到appium server
-------------


[MJSONWP] Calling AppiumDriver.findElement() with args: ["-android uiautomator","new UiSelector().text(\"登 录\")","e6cb349d-d651-4531-aa87-038d3db650f9"]
-------------
MJSONWP对appium server的内容做了解析
-------------


[BaseDriver] Valid locator strategies for this request: xpath, id, class name, accessibility id, -android uiautomator
[BaseDriver] Valid locator strategies for this request: xpath, id, class name, accessibility id, -android uiautomator
[BaseDriver] Waiting up to 0 ms for condition
-------------
BaseDriver对MJSONWP的消息进行了处理
并且进行了格式的校验
-------------


[AndroidBootstrap] Sending command to android: {"cmd":"action","action":"find","params":{"strategy":"-android uiautomator","selector":"new UiSelector().text(\"登 录\")","context":"","multiple":false}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got data from client: {"cmd":"action","action":"find","params":{"strategy":"-android uiautomator","selector":"new UiSelector().text(\"登 录\")","context":"","multiple":false}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command of type ACTION
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command action: find
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Finding 'new UiSelector().text("登 录")' using 'ANDROID_UIAUTOMATOR' with the contextId: '' multiple: false
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Parsing selector: new UiSelector().text("登 录")
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] UiSelector coerce type: class java.lang.String arg: "登 录"
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Using: UiSelector[TEXT=登 录]
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Returning result: {"status":0,"value":{"ELEMENT":"1"}}
[AndroidBootstrap] Received command result from bootstrap
-------------
BaseDriver将消息发送给AndroidBootstrap
我们可以把AndroidBootstrap想象成一个服务器
其中的UIAutomator是它内部的一个程序
当AndroidBootstrap接收到指令，就会调用UIAutomator进行相应的动作
-------------


[MJSONWP] Responding to  client with driver.findElement() result: {"ELEMENT":"1"}
[HTTP] <-- POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/element 200 123 ms - 87 
-------------
将结果返回给MJSONWP，同时交给appium server
对于找到的控件，UIAutomator会在自己的哈希表，刚刚找到的那个控件会给它分配一个id    1
以后每次找到都会进行+1操作
* 此时BaseDriver没有参与工作
-------------


[HTTP] --> POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/element/1/click {"sessionId":"e6cb349d-d651-4531-aa87-038d3db650f9","id":"1"}
[MJSONWP] Calling AppiumDriver.click() with args: ["1","e6cb349d-d651-4531-aa87-038d3db650f9"]
[AndroidBootstrap] Sending command to android: {"cmd":"action","action":"element:click","params":{"elementId":"1"}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got data from client: {"cmd":"action","action":"element:click","params":{"elementId":"1"}}
[AndroidBootoidBootstrap] [BOOTSTRAP LOG] [debug] Got command action: click
[AndroidBootstrap] Received command result from bootstrap
[MJSONWP] Responding to client with driver.click() result: true
[HTTP] <-- POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/element/1/click 200 677 ms - 76 
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Returning result: {"status":0,"value":true}
-------------
执行点击操作
有意思的是整个流程BaseDriver也没有参与工作
那么你知道BaseDriver会在什么时候调用了吗？:)
-------------



[HTTP] --> POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/element {"using":"id","value":"com.tencent.mobileqq:id/password","sessionId":"e6cb349d-d651-4531-aa87-038d3db650f9"}
[MJSONWP] Calling AppiumDriver.findElement() with args: ["id","com.tencent.mobileqq:id/password","e6cb349d-d651-4531-aa87-038d3db650f9"]
[BaseDriver] Valid locator strategies for this request: xpath, id, class name, accessibility id, -android uiautomator
[BaseDriver] Valid locator strategies for this request: xpath, id, class name, accessibility id, -android uiautomator
[BaseDriver] Waiting up to 0 ms for condition
[AndroidBootstrap] Sending command to android: {"cmd":"action","action":"find","params":{"strategy":"id","selector":"com.tencent.mobileqq:id/password","context":"","multiple":false}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got data from client: {"cmd":"action","action":"find","params":{"strategy":"id","selector":"com.tencent.mobileqq:id/password","context":"","multiple":false}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command of type ACTION
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command action: find
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Finding 'com.tencent.mobileqq:id/password' using 'ID' with the contextId: '' multiple: false
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Using: UiSelector[INSTANCE=0, RESOURCE_ID=com.tencent.mobileqq:id/password]
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Returning result: {"status":0,"value":{"ELEMENT":"2"}}
[AndroidBootstrap] Received command result from bootstrap
[MJSONWP] Responding to client with driver.findElement() result: {"ELEMENT":"2"}
[HTTP] <-- POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/element 200 597 ms - 87 
-------------
寻找password输入框操作
可以看到返回的ELEMENT进行了自增+1的操作
-------------



[HTTP] --> POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/appium/element/2/value {"value":["password"],"sessionId":"e6cb349d-d651-4531-aa87-038d3db650f9","id":"2"}
[MJSONWP] Calling AppiumDriver.setValueImmediate() with args: [["password"],"2","e6cb349d-d651-4531-aa87-038d3db650f9"]
[AndroidBootstrap] Sending command to android: {"cmd":"action","action":"element:click","params":{"elementId":"2"}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got data from client: {"cmd":"action","action":"element:click","params":{"elementId":"2"}}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command of type ACTION
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command action: click
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Returning result: {"status":0,"value":true}
[AndroidBootstrap] Received command result from bootstrap
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","input","text","password"]
[MJSONWP] Responding to client with driver.setValueImmediate() result: null
[HTTP] <-- POST /wd/hub/session/e6cb349d-d651-4531-aa87-038d3db650f9/appium/element/2/value 200 1518 ms - 76 
-------------
执行输入密码功能
可以看到传递了一个element为2的参与用在标识我们找到的密码框控件
同时，密码的输入是调用的adb shell的input指令进行的
-------------




[BaseDriver] Shutting down because we waited 60 seconds for a command
[AndroidDriver] Shutting down Android driver
[Appium] Closing session, cause was 'New Command Timeout of 60 seconds expired. Try customizing the timeout using the 'newCommandTimeout' desired capability'
[Appium] Removing session e6cb349d-d651-4531-aa87-038d3db650f9 from our master session list
-------------
由于60秒没有进行操作
在session list中删除了session
-------------


[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","am","force-stop","com.tencent.mobileqq"]
-------------
强制关闭了qq
-------------


[ADB] Pressing the HOME 
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","input","keyevent",3]
-------------
调用keyevent进入了home界面
-------------


[AndroidBootstrap] Sending command to android: {"cmd":"shutdown"}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got data from client: {"cmd":"shutdown"}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Got command of type SHUTDOWN
[AndroidBootstrap] Received command result from bootstrap
[UiAutomator] Shutting down UiAutomator
[UiAutomator] Moving to state 'stopping'
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Returning result: {"status":0,"value":"OK, shutting down"}
[AndroidBootstrap] [BOOTSTRAP LOG] [debug] Closed client connection
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: numtests=1
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: stream=.
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: id=UiAutomatorTestRunner
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: test=testRunServer
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: class=io.appium.android.bootstrap.Bootstrap
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: current=1
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS_CODE: 0
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS: stream=
[AndroidBootstrap] [UIAUTO STDOUT] Test results for WatcherResultPrinter=.
[AndroidBootstrap] [UIAUTO STDOUT] Time: 73.533
[AndroidBootstrap] [UIAUTO STDOUT] OK (1 test)
[AndroidBootstrap] [UIAUTO STDOUT] INSTRUMENTATION_STATUS_CODE: -1
[UiAutomator] UiAutomator shut down normally
[UiAutomator] Moving to state 'stopped'
[ADB] Attempting to kill all uiautomator processes
[ADB] Getting all processes with uiautomator
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","ps"]
[ADB] No uiautomator process found to kill, continuing...
[UiAutomator] Moving to state 'stopped'
-------------
关闭所有AndroidBootstrap中的UiAutomator服务
-------------


[Logcat] Stopping logcat capture
[ADB] Getting connected devices...
[ADB] 1 device(s) connected
[ADB] Running 'D:\study\zidonghua\appliume\android-sdk\android-sdk-windows\platform-tools\adb.exe' with args: ["-P",5037,"-s","emulator-5554","shell","am","force-stop","io.appium.unlock"]
-------------
关闭所有的io.appium.unlock程序
比较奇怪的是，我们从来没有调用过io.appium.unlock程序
笔者也不知道为什么要调用一次关闭命令
-------------



[AndroidDriver] Not cleaning generated files. Add `clearSystemFiles` capability if wanted.
```

#### 原理浅析
- 可以看到，整个流程经历了这么几个步骤
- 前置操作
```
启动appium server
启动adb服务
```
- 建立session
```
client：将**driver = webdriver.Remote("http://127.0.0.1:4723/wd/hub",desc)**代码转为http请求发送给appium server
↓
appium server对接受到的http请求进行解析发送给MJSONWP进行格式处理
↓
MJSONWP:处理完格式转发给BaseDriver进行字段校验
↓
BaseDriver:校验完之后执行指令新建一个AndroidDriver驱动程序
↓
ADB，AndroidDriver:  通过两者找到我们的设备
↓
进行一系列的检查操作:
- 检查是否安装了软件包
- 再次检查能否正确操作设备
- 安装io.appium.settings和unlock helper两个app
检查完毕之后adb启动了4727端口，用于后续的AndroidBootstrap新建
↓
AndroidBootstrap新建：
- 将AppiumBootstrap.jar的jar包从本机传输到设备上
- 查找当前设备上是否已经执行了AppiumBootstrap进程，如果有就kill掉
- 启动AndroidBootstrap，并启动UIAutomator服务
↓
通过adb执行启动app
同时Appium生成一个session
↓
BaseDriver：打印启动日志
↓
MJSONWP：对传递过来的信息进行格式处理
↓
HTTP：获取到设备信息并和生成的session进行绑定
```
- find代码执行
```
HTTP
↓
MJSONWP
↓
BaseDriver
↓
AndroidBootstrap
↓
MJSONWP
↓
HTTP
```
- click和input代码执行
```
HTTP
↓
MJSONWP
↓
AndroidBootstrap
↓
ADB
↓
MJSONWP
↓
HTTP
```