# RN笔记

## 一. 常用命令

- 比如我们希望查看RN的所有历史版本，可以在命令行中输入：

> `npm view react-native versions -json`

- 创建工程并指定版本:

> `react-native init 工程名字 -source react-native@0.55.4 (无效)`
> `react-native init 工程名字 --version 0.55.4`

- 在项目中运行npm intall --save命令来执行安装react-native新版本：

> `npm install --save react-native@版本号`

- [ReactNative用指定的真机/模拟器运行项目](https://blog.csdn.net/u011215669/article/details/80391044)

> `react-native run-ios --simulator "iPhone 7 Plus”`
`双引号也是需要的，做Android不熟悉的可以先将需要的模拟器跑起来，模拟器下面会显示型号 + 系统版本，系统版本不需要`

- 使用真机运行项目：（需要提前安装工具：npm i -g ios-deploy）

> `react-native run-ios --device "'YooHoh's iPhone X"`

- 查看当前可用的所有设备/模拟器列表:

> `xcrun simctl list devices`
`我平时主要是Android真机的时候会用到，iOS真机我就直接Xcode跑了，一般是先adb devices检查下能否识别到Android真机，识别不到的话，看看USB设置，物理连接`

- 链接库

> `react-native link`

- [react-native常用操作指令](https://blog.csdn.net/potato512/article/details/80249995)

> ` http://localhost:8081/debugger-ui/ `

- Android调出菜单的指令

> `adb shell input keyevent 82`

---------

## 二、iOS端build 遇到的报错

- 报错1：

```bash
An error was encountered processing the command (domain=NSPOSIXErrorDomain, code=22):
Failed to install the requested application
The bundle identifier of the application could not be determined.
Ensure that the application's Info.plist contains a value for CFBundleIdentifier.
Print: Entry, ":CFBundleIdentifier", Does Not Exist
Command failed: /usr/libexec/PlistBuddy -c Print:CFBundleIdentifier build/Build/Products/Debug-iphonesimulator/travelProject.app/Info.plist
Print: Entry, ":CFBundleIdentifier", Does Not Exist

```

>尝试方案:  
> 基本思路： 先确保依赖包没问题，可以手动下载替换， 确保依赖包完整后， 清缓存，npm重新安装依赖

[关于Entry, ":CFBundleIdentifier", Does Not Exist的解决方法](https://blog.csdn.net/SummerCloudXT/article/details/80795465)  

> 清项目依赖包， 也可以手动删除`node_modules`文件夹下内容

```bash
cd node_modules/react-native/third-party/glog-0.3.4
../../scripts/ios-configure-glog.sh
```

>[解决CFBundleIdentifier", Does Not Exist](https://blog.csdn.net/yuanshuaicsdn/article/details/77853941)

```bash
mac环境下，在命令行中run-ios构建时报错：CFBundleIdentifier", Does Not Exist
打开XCode，进入.xcodeproj文件，运行，编译时报错:'boost/iterator/iterator_adaptor.hpp' file not found’
经过多次测试，这个问题只在react native 0.45.0及以后的版本中出现。

在网上找到解决方法如下：

这个问题产生原因：
/Users/你的用户名/.rncache中boost_1_63_0.tar.gz，double-conversion-1.1.5.tar.gz，folly-2016.09.26.00.tar.gz，glog-0.3.4.tar.gz文件不完整。或者node_modules/react-native/third-party 文件不完整。

具体操作：
1、删除/user/你的用户名/.rncache目录下的boost_1_63_0。重新下载,下载网址http://www.boost.org/users/history/version_1_63_0.html
2、打开命令行工具，在项目目录下输入rm -rf node_modules && rm -rf ~/.rncache && yarn
3、npm install  
4、react-native upgrade  
```

- 报错2：

```bash
Libtool /Users/Shen/Desktop/travel/travelProject/ios/build/Build/Products/Debug-iphonesimulator/libRCTWebSocket.a normal x86_64 (1 failure)
```

> [Xcode 10 libfishhook.a cannot be found](https://github.com/facebook/react-native/issues/19569)

 根据提示去路径里看了下，唯独缺少`libRCTWebSocket.a`静态库， 打开Xcode工程在Lib中找到RCTWebSocket项目，在`Link Binary With Libraries`下看到`libfishhook.a`图标状态有点虚，移除重新导入，再次执行`react-native run-ios` 解决。

- 报错3： `'config.h' file not found`

> 我通过备份项目，进到了路径`Macintosh HD⁩ ▸ ⁨用户⁩ ▸ ⁨Shen⁩ ▸ ⁨桌面⁩ ▸ ⁨GYYX⁩ ▸ ⁨travel⁩ ▸ ⁨travelProject⁩ ▸ ⁨node_modules⁩ ▸ ⁨react-native⁩ ▸ ⁨third-party⁩ ▸ ⁨glog-0.3.4⁩`比对，出问题的项目的确是缺少错误中的文件，会有一个`config.h.in`文件，起初我通过另外正常项目将文件复制了一份，但是又出现了其他文件缺失的问题

[third-party: 'config.h' file not found 解决方案](https://blog.csdn.net/qq_33323251/article/details/79983779)

- 报错4： `make sure you're running a packager server or have included a .jsbundle file in your application bundle`

  1. 删除build文件夹
  2. 关闭VPN
  3. 删除node_modules/third-party文件夹

- 报错5: `React Native version mismatch`

> [React Native version mismatch 错误](https://blog.csdn.net/awy1988/article/details/80336913)

### 2019.01.07

> - [Props(属性)](https://reactnative.cn/docs/props/)
> - 自定义组件
> - [State(状态)](https://reactnative.cn/docs/style/)
> - [样式](https://reactnative.cn/docs/style/)
> - [高度与宽度（指定宽高和弹性宽高）](https://reactnative.cn/docs/height-and-width/)
> - [Flexbox布局（Flex Direction、Justify Content和Align Items)](https://reactnative.cn/docs/flexbox/)

## 三、Android端环境配置

- 打开__Android__ __Studio__ 报错`Unable to access Android SDK add-on list`

> 原因： 检测到电脑没有SDK, 通过AS管理SDK勾选安装后，配置环境变量

```bash
vi ~/.bash_profile
```

> 通过SD管理SDK安装JDK等资源，可以通过AS看到安装路径

```bash
export ANDROID_HOME=~/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
//本机新账号下默认没有.bash_profile，按照视频讲解在补充上面两个环境后也加上了
export ANDROID_SDK=$ANDROID_HOME
export ANDROID_NDK=/usr/local/Cellar/android-ndk-r10e/r10c
```

> 使配置生效或者重启电脑

```bash
source ~/.bash_profile

```

> 检查配置是否生效

```bash
echo $Android/sdk
```

- 配置好路径后， 直接`react-native run-android`，下载一些依赖包后报错

```bash
FAILURE: Build failed with an exception.
* What went wrong:
Execution failed for task ':app:installDebug'.
com.android.builder.testing.api.DeviceException: No connected devices!
```

> 原因：
> iOS通过__run-android__会自动启动模拟器，Android运行__run-android__不会自动启动模拟器
> 出现这一行，表示环境已经OK， 下一步需要启动模拟器或连接设备

- 下面的报错信息是一个一般性的提示信息，任何报错都可能会出现这段描述

```bash
Could not install the app on the device, read the error above for details.
Make sure you have an Android emulator running or a device connected and have
set up your Android development environment:
https://facebook.github.io/react-native/docs/getting-started.html
```

- 接下来就是通过AS打开工程，创建一个模拟器，并启动模拟器，然后再通过 __react-native__ __run-android__ 启动android项目

- SDK版本问题

```bash
* What went wrong:
A problem occurred configuring project ':app'.
> Could not resolve all dependencies for configuration ':app:_debugApk'.
   > A problem occurred configuring project ':react-native-webview-bridge-updated'.
      > The SDK Build Tools revision (23.0.1) is too low for project ':react-native-webview-bridge-updated'. Minimum required is 25.0.0
```

>去路径 `Macintosh HD⁩ ▸ ⁨用户⁩ ▸ ⁨Shen⁩ ▸ ⁨桌面⁩ ▸ ⁨GYYX⁩ ▸ ⁨travel⁩ ▸ ⁨travelProject⁩ ▸ ⁨node_modules⁩ ▸ ⁨react-native-webview-bridge-updated⁩`下找到`package.json`，将版本修改为要求的版本
保存后重新执行run，可能会有多个文件提示版本问题，根据提示进行修改

- NDK is missing a "platforms" directory

> -[RN项目报错：NDK is missing a "platforms" directory.，解决方法](https://blog.csdn.net/qq_28325423/article/details/80636310)

- No toolchains found in the NDK toolchains folder for ABI with prefix

> -[No toolchains found in the NDK toolchains folder for ABI with prefix](https://www.jianshu.com/p/fd3d49c7f1f8)

- Could not find method implementation() for arguments...

```bash
* What went wrong:
A problem occurred evaluating project ':react-native-fast-image'.
> Could not find method implementation() for arguments [com.facebook.react:react-native:+] on object of type org.gradle.api.internal.artifacts.dsl.dependencies.DefaultDependencyHandler.
```

> 打开build.gradle文件， 将implementation替换成compile

### 2019.01.08

> - [处理文本输入](https://reactnative.cn/docs/handling-text-input/)
> - [处理触摸事件](https://reactnative.cn/docs/handling-touches/)
> - [使用滚动视图](http://reactnative.cn/docs/0.55/using-a-scrollview/)
> - [使用长列表](https://reactnative.cn/docs/0.55/using-a-listview/)
> - [网络](https://reactnative.cn/docs/0.55/network/)

## 四、相关资料

- [npm package.json属性详解](http://www.cnblogs.com/tzyy/p/5193811.html)

- `^`和`~`区别:

> 波浪符号（~）：他会更新到当前minor version（也就是中间的那位数字）中最新的版本。
> 放到我们的例子中就是：body-parser:~1.15.2，这个库会去匹配更新到1.15.x的最新版本，
> 如果出了一个新的版本为1.16.0，则不会自动升级。
> 波浪符号是曾经npm安装时候的默认符号，现在已经变为了插入符号。  
> 插入符号（^）：这个符号就显得非常的灵活了，他将会把当前库的版本更新到当前major version
（也就是第一位数字）中最新的版本。
放到我们的例子中就是：bluebird:^3.3.4，这个库会去匹配3.x.x中最新的版本，
但是他不会自动更新到4.0.0。

- [关于npm audit fix](https://blog.csdn.net/weixin_40817115/article/details/81007774)

- [NPM install -save 和 -save-dev](https://www.cnblogs.com/limitcode/p/7906447.html)

```bash
npm install moduleName # 安装模块到项目目录下
npm install -g moduleName # -g 的意思是将模块安装到全局，具体安装到磁盘哪个位置，要看 npm config prefix 的位置。
npm install -save moduleName # -save 的意思是将模块安装到项目目录下，并在package文件的dependencies节点写入依赖。
npm install -save-dev moduleName # -save-dev 的意思是将模块安装到项目目录下，并在package文件的devDependencies节点写入依赖。
```

- [关于RN组件的导出export和export default](https://blog.csdn.net/it_talk/article/details/53156599)  

- [RN export的那些事儿](https://www.jianshu.com/p/e60ddfd0bd62)

- [devDependencies和dependencies的区别](https://blog.csdn.net/achenyuan/article/details/80899783)

- [jest单元测试](https://jestjs.io/docs/en/using-matchers)  
