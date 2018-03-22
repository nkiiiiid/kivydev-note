# kivy打包apk虚拟机搭建指南

## 前言
这个文档其实是去年搭建虚拟机写的，因为各种原因一直没有发布出来，今天在Kivy中国开发者交流群（群号**534622543**）@lr大佬向我要这个，我于是下定决定把拖延症抢救下，对原来的文档稍作修正发布出来，希望对一心想要自己搭建打包环境却不得其法的朋友有一些帮助，也希望各位路过的大佬不吝赐教，不足之处望请斧正，如果能起到抛砖引玉的效果，本npc就很满足了。

<img src="https://i.imgur.com/sjKEzMT.jpg" width = "280" height = "150" alt="npc" align=center />

## linux架设打包环境
### 0x01 系统配置
原本是用ubuntu16.04 32位，由于不能使用p4a正确打包，这里尝试使用64位。
刚刚装好Ubuntu无法使用root账户，要给root设置下密码。
`sudo passwd root`
这里root密码我设置为root。
切换到root，安装新立得
```
apt-get update             #先更新下源
apt-get install synaptic   #这里可能会出错，需要运行下apt-get –f install 修复下
```
打开新立得，我这里安装个xfce桌面，unity实在是卡，搜索xfce，然后mark、apply，注销系统，在登录界面选择xfce session即可。

关于增强工具，xfce的增强工具安装的是virtualbox-guest-dkms，安装x11不能使用，并且会不响应鼠标动作。


### 0x02 安装kivy
这里安装最新版的kivy 
使用新立得安装：python-pip \
    build-essential \
    git \
    python \
    python-dev \
    ffmpeg \
    libsdl2-dev \
    libsdl2-image-dev \
    libsdl2-mixer-dev \
    libsdl2-ttf-dev \
    libportmidi-dev \
    libswscale-dev \
    libavformat-dev \
    libavcodec-dev \
    zlib1g-dev

现在试运行一个程序
`python /usr/share/kivi-examples/guide/firstwidget/1_skeleton.py`
这里我们对kivy-examples修改下权限，因为默认当前用户对kivy-examples没有写的权限。
`sudo chmod -R 775  /usr/share/kivy-examples`


### 0x03 安装打包apk需要的一些库
参考http://python-for-android.readthedocs.org/en/latest/prerequisites/
使用新立得安装**build-essential** ccache git zlib1g-dev **libncurses5 libstdc++6 zlib1g:i386 **openjdk-8-jdk **unzip** ant    （标记粗体的是本来就存在的）、patch libsdl1.2-dev、libsdl2-dev、autoconf、libtool


### 0x04 配置环境变量

> **关于linux环境变量目录的一些前置说明**
> /etc/profile:此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行.
> 并从/etc/profile.d目录的配置文件中搜集shell的设置.
> /etc/bashrc:为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.
> ~/.bash_profile:每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该文件仅仅执行一次!默认情况下,他设置一些环境变量,执行用户的.bashrc文件.
> ~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该文件被读取.
> ~/.bash_logout:当每次退出系统(退出bash shell)时,执行该文件.


因此在/etc/profile.d目录中新建一个脚本**kivydev.sh**，添加如下变量：
```
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-i386
export JDK_HOME=$JAVA_HOME
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/jre/lib/rt.jar:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```
> 注意：linux环境变量是用冒号隔开的，环境变量设置如果出现格式错误，重启后会造成无法登陆桌面，这个时候要在命令行下来救了，在登录界面按下ctrl+alt+f1进入命令行，这里由于环境变量全部失效，所以su之类的命令都要带上完整路径才能运行。把环境变量改对了就可以了。


### 0x05 安装sdk、ndk、pythonforandroid
从http://www.androiddevtools.cn/ 下载android sdk tool（下载下来解压后是android-sdk-linux）、sdk platform-tools（这里我们都下载最新版本）。把sdk platform-tools放到android-sdk-linux目录下。这里为了方便管理sdk、ndk，我在当前用户目录下新建了一个目录/home/kivydev/andr/，把android-sdk-2移到andr目录下。

**安装build-tools**
运行/home/kivydev/andr/android-sdk-20/tools/android，即android sdk manager。会得到如下界面5-1：

<img src="https://i.imgur.com/opVUtSw.png" width = "500" height = "350" alt="5-1" align=center />

点击菜单tools—options，按照下图5-2所示，填写代理服务器：mirrors.neusoft.edu.cn，端口：80，勾选force项。（让https链接使用http协议。）

<img src="https://i.imgur.com/FmBGYui.png" width = "300" height = "300" alt="5-2" align=center />

重启android sdk manager，如图5-1所示，现在可以开始更新tools、platform-tools和安装build-tools，软件会默认选中安装android N/6.0等等系统，现在先把他们取消掉。勾选tools、platform-tools、 build-tools，点击右下角的install 3 packages，会弹出一个接受协议的对话框如图5-3，点击accept license，点击install即可。（可能和这里的图显示不同，这图是事后补的）

<img src="https://i.imgur.com/ceXpGuH.png" width = "300" height = "300" alt="5-2" align=center />

以类似的步骤下载安装android 4.4.2下面的sdk platform


**安装ndk**
从http://www.androiddevtools.cn/ 下载


**安装pythonforandroid**
***切换到root下***(这很重要）
pip install python-for-android         

**安装virtualenv**
使用新立得下载python-virtualenv


### 0x06 添加sdk、ndk、ant环境变量
在/etc/profile.d/kivydev.sh中添加如下变量：
```
export ANDROIDSDK= /home/kivydev/andr/android-sdk-20
export ANDROIDNDK=/home/kivydev/andr/android-ndk-r9c
export ANDROIDNDKVER=r13
export ANDROIDAPI=19
export ANT_HOME=/home/kivydev/andr/apache-ant-1.9.4
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$ANDROIDSDK/tools:$ANDROIDSDK/platform-tools:$ANT_HOME/bin:$PATH
```
重启或者注销

> 这里发现一个问题，adb不能使用，提示是bash: ./adb: cannot execute binary file: Exec format error，经过搜索发现是现在的platform-tools已经不支持32位系统了，所以我们要用apt install android-tools-adb安装32位的来替换sdk中的adb。fastboot同理，apt install android-tools-fastboot。然后把/usr/bin下的adb和fastboot复制到platform-tools下面。


### 0x07 测试打包
2017.5.31更新
启动Vbox突然出现

![](https://i.imgur.com/DtiJGRx.png)

这个问题普遍存在于各种情形下，我这里虚拟机之前是正常的，自从电脑打了补丁后就出现这个错误，经查，有效的解决方法是重新注册VboxC.dll：
切换到Vbox的安装目录下，运行cmd命令
```
VBoxSVC /ReRegServer
regsvr32 VBoxC.dll
```
会看到如下对话框，说明注册成功，现在启动Vbox就正常了。
![](https://i.imgur.com/J8rULNH.png)

在kivy目录下新建一个pfatest目录，将accordion例子复制到目录下，重命名为main.py。运行命令：
```
su -
p4a apk --requirements=kivy --private /home/kivydev/kivy/pfatest/ --package=com.pfaapp.test --name="test" --version=1.0 --bootstrap=sdl2
```
编译成功，经过与之前的编译方式对比应该是pfa安装在root下才能正常编译，否则会出现各种问题。

<del>打包出来的apk有9m大小，运行时启动loading图像会卡屏，原因不明。<del>

p4a使用sdl2打包的参数说明
```
--requirements，需要的第三方库
--private，项目目录
--package，app包名
--name，app名称
--version，版本号
--bootstrap，app后端

-- permission，app权限
--window，不全屏
--orientation，屏幕方向，支持portait, landscape, sensor
--icon，app图标路径，png格式
--presplash，app启动时的加载图像
--presplash-color，app启动时加载界面背景，可以是#rrggbb格式或者red，green，blue等等。
--wakelock，取消系统休眠
--blacklist，不打包进apk的文件
--whitelist，打包进apk的文件
--add-jar，要打包进的jar，如果有多个jar则要逐个使用该参数
--meta-data，元数据
```

p4a会自动查找当前目录下.p4a文件作为配置文件，该文件里面可以包含各类参数。
```
--requirements=kivy 
--private . 
--package=com.pfaapp.test 
--name="test" 
--version=1.0 
--bootstrap=sdl2
```


## 使用buildozer打包
- 所有包的安装都在root下进行，因为在Ubuntu下，普通用户pip（即使是sudo pip）会把包安装到当前用户目录下，不在系统目录下，造成无法在系统环境变量path下找到包。
- 先安装buildozer，`pip install buildozer`
- 需要安装的依赖库：zlib1g-dev、git、cython（注意必须安装与kivy对应的版本，我这里kivy是1.9.0，cython应当是0.21.2）、openjdk-8-jdk
- 然后切换回普通用户权限  
- 在项目目录下运行 `buildozer init`  
- 打开spec文件，把log_level=1改成2  
- 运行 `buildozer android_new debug`  
它会根据recipe下载相应的包，通常一个kivy包括Recipe build order is ['python2', 'sdl2_image', 'sdl2_mixer', 'sdl2_ttf', 'sdl2', 'six', 'pyjnius', u'kivy']



## 出现错误及解决办法:
```
ERROR: The colorama Python module could not be found, please install version 0.3.3 or higher
ERROR: The appdirs Python module could not be found, please install it.
ERROR: The sh Python module could not be found, please install version 1.10 or higher
ERROR: The jinja2 Python module could not be found, please install it.
ERROR: The six Python module could not be found, please install it.
python-for-android is exiting due to the errors above.
```
解决：用pip安装colorama、appdirs、sh、jinja2、six


`Aidl not found, please install it.`
解决：经查是buildozer使用的Android sdk（位于/home/crossx/.buildozer/android/platform/android-sdk-20/）下没有安装build-tools等工具和sdk。使用/home/crossx/.buildozer/android/platform/android-sdk-20/tools/下的Android安装以上工具即可。

`AttributeError: 'Context' object has no attribute 'hostpython'`
解决：在spec的requestments加上python2

打包的时候可能会出网络错误，这就是你的网络问题了，我多尝试打包命令几次就好了。

由于默认的spec文件设置并不符合自己的习惯，我们虽然可以在生成spec后自己修改或者复制已经修改过的其他项目的spec文件到新的项目中使用，但是这总是不方便，最好的方法是修改buildozer自带的默认spec，让他每次生成的spec符合我们的习惯。默认的spec文件位于/usr/local/lib/python2.7/dist-packages/buildozer/default.spec，只要修改它就可以自定义生成的spec。

**查找默认spec文件的方法。**
通过分析buildozer命令脚本—client类（/usr/local/lib/python2.7/dist-packages/buildozer/scripts/client.py）----Buildozer类（/usr/local/lib/python2.7/dist-packages/buildozer/__init__.py），搜索spec，在第1054行找到创建spec文件的语句。

**webview打包问题**
webview使用了jnius和Android模块，打包后在安卓4.4.4真机上运行没问题，但是在安卓5.1和6上闪退。在genymotion模拟器4.4.4上运行闪退。
模拟器闪退的日志
```
D:\Program Files\Genymobile\Genymotion\tools>adb logcat | find /i "myapp"
I/PackageManager(  409): Package installed with second ABI Library: 10059org.tes
t.myapp
I/ActivityManager(  409): START u0 {act=android.intent.action.MAIN cat=[android.
intent.category.LAUNCHER] flg=0x10200000 cmp=org.test.myapp/org.kivy.android.Pyt
honActivity} from pid 732
I/ActivityManager(  409): Start proc org.test.myapp for activity org.test.myapp/
org.kivy.android.PythonActivity: pid=1303 uid=10059 gids={50059, 1028, 1015, 300
3}
D/dalvikvm( 1303): Trying to load lib /data/app-lib/org.test.myapp-1/libSDL2.so
0xa5024008
F/libc    ( 1303): Fatal signal 11 (SIGSEGV) at 0x000000b4 (code=1), thread 1303
 (org.test.myapp)
I/DEBUG   (  157): pid: 1303, tid: 1303, name: org.test.myapp  >>> org.test.myap
p <<<
W/ActivityManager(  409):   Force finishing activity org.test.myapp/org.kivy.and
roid.PythonActivity
I/WindowState(  409): WIN DEATH: Window{529a00bc u0 org.test.myapp/org.kivy.andr
oid.PythonActivity}
I/ActivityManager(  409): Process org.test.myapp (pid 1303) has died.
I/WindowManager(  409): Screen frozen for +1s454ms due to Window{5294a138 u0 Sta
rting org.test.myapp}
```
其中fatal错误是个随机地址错误，搜索后得到的原因五花八门，有内存访问越界、Androidmanifest.xml权限修改等等。



by  海森堡测不准面包
2018.3.22 修正发布
