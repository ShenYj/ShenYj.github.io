---
layout: post
title: "React-Native 踩坑日记"
date: 2020-08-31
---

# RN踩坑

- 环境错误  

  第一次接触`RN`，肯定是从配置环境入手，完成了双端的编译运行，这仅仅是个开始
关于RN的各种报错原因及解决方案，可以参考这位大神的总结  
[React Native开发错误警告处理总结（已解决 ！持续更新](https://www.jianshu.com/p/98c8f2a970eb)  

  补充:  
如果偶尔遇到了一些网上搜不到的报错信息，或者尝试了网上的方法无效，不妨关掉Debug模式，关闭调试浏览器页面，关闭模拟器，关闭js服务，重新run一次，偶尔的情况下，不做任何调整就能恢复正常，还有调试模式下无法断点拦截，或者断点错位，都可以试试  
[我在配置RN环境中遇到的错误及解决方案总结](https://www.jianshu.com/p/9486226eefcc)

- `flex`布局  

  第一次使用`flex`布局，上手写了个非常非常简单的界面（修改密码功能），写完后跑在`Android`机上，键盘弹起后，发现控件的高度被挤压了.....  
  - 解决方案:  
  - 设定高度  

- `display`: 布局中使用了绝对布局，那么`display`将在Android下失效  

  解决方案：

  设定控件的宽高，来达到控件显隐的控制效果

- `TextInput`: iOS下`TextInput`中文`BUG`

  解决方案:

  RN版本还处于不断更新完善中，项目中一旦决定采用了某个指定版本，一般不会轻易升级RN版本，例如我们项目使用的是 `v55.4`，`iOS`下 `TextInput`输入中文的`BUG`相信不少人应该遇到过了，网上也有很多临时解决方法，但我感觉都不太完善，按照网上的方法可以解决原生输入法的问题，但是在搜狗输入法下还是会有点问题，在后续的RN版本中已经修复了这个BUG，由于项目不打算升级RN版本，采取了修改源码的方式来修复这个BUG，麻烦的就是，要修改开发环境的源码，同时还要修改构建机源码，避免今后构建机```npm install```后问题复现，还要在脚本中特殊处理，避免今后出现问题

  [TextInput Fix controlled TextInput for Chinese (and other languages) #18456](https://github.com/facebook/react-native/pull/18456/files#diff-8eb50d68d87e28556c034717cd58a86e)

- `stickyHeaderIndices`会导致后一个控件二次调用`componentDidMount`方法

  用过RN的人不一定熟悉这个属性，它是ScrollView用于控制内部某个子控件是否悬挂的，如果你的页面没有什么特殊场景，可能不会造成什么影响，然后我们需要悬挂的是个`WebView`，内嵌了一个H5的 `MapView`，别问我为啥这样，因为这样就不需要客户端挂VPN了，同时这个控件下面的界面稍微复杂一点，是单独封装了一个用于展示路线的控件，包括途径地点，两点之间的出行方式，详细步骤等介绍

  解决方案:

  调整控件顺序，尽量找一个即便是`componentDidMount`被多次调用了，也不会产生什么影响的控件在悬挂控件下面，如`Modal`的控件

- `FlatList`的`ListFooterComponent`

  列表中常见的场景，当列表数据全部加载完毕，会提示“全部加载完毕”， 先描述下我的实现方式
界面顶部一个导航栏，下面一个列表触底，界面布局没有使用`Flex`， 而是通过屏幕的高度直接限定
当显示"全部加载完毕"时，`iOS`下正常，而`Android`下没有显示，正当我一脸懵逼的时候，给`ListFooterComponent`加了个背景色，给`FlatList`加了个`marginbottom`，发现`Android`的`ListFooterComponent`这货超出屏幕了

  解决方案:

  起初的解决方案是，在Android下加了一个间距，最后是设置了`render`中最外层`View`的`flex`

- [react-native-view-shot](http://npm.taobao.org/package/react-native-view-shot)截图，`Android` 下报错:

  `Trying to resolve view with tag XXXX which doesn't exist`

  `on Android, getting "Trying to resolve view with tag '{tagID}' which doesn't exist"`

  - you need to make sure collapsable is set to false if you want to snapshot a View. Some content might even need to be wrapped into such <View collapsable={false}> to actually make them snapshotable! Otherwise that view won't reflect any UI View. (found by @gaguirre)

  Alternatively, you can use the ViewShot component that will have collapsable={false} set to solve this problem.

  - 先来说下界面结构：

   -ScrollView  
   ----WebView 需要截取的控件  
   ----View    绝对布局的一个遮罩层  
   -ScrollView

   业务需求，整个页面都是H5写的，当点击截屏的时候，`H5`会将`WebView`中的`H5`页面完全展开，返回一个高度，然后RN这边根据高度截取整个H5页面，本身我是一个`iOS Developer`，按理讲WebView里面也是有个`ScrollView`的，但是截屏后死活不对，只有一屏大小；于是将外层`View`节点改成了`ScrollView`，当截屏的时候，重新设置`WebView`的高度，将`ScrollView`撑起来，`iOS`可以了，但是`Android`上一跑，就红屏了，报这个错误，按照方法试过无效，也有说设置背景色的，仍然不行，最后调整了下布局，直接截`ScrollView`；这还没完，`iOS`模拟器上截图模糊，而`Android`一直是真机运行没有此问题，后来改用`iPhone`真机调试，没有了模糊的问题，但当`H5`页面展开过长，截取的图片为纯白色空白页面，起初怀疑和`ScrollView`嵌套`Webview`有关，但通过`demo`排除，暂时留存，打算后面再通过分段截图的方式处理

- `ReacNative`：`iOS`正常，`Android` 报错`Cannot add a child that doesn't have a YogaNode to a parent without a measure function！`

  写好一个界面，iOS跑完全没问题，Android直接Crash，提示`ReacNative：报错Cannot add a child that doesn't have a YogaNode to a parent without a measure ...`这类信息，一脸懵逼，网上搜到了这篇文章：[传送门](https://blog.csdn.net/qq_32312317/article/details/80769453)，因为界面内的所有模块都是抽出去写的，然后开始定位排查，最终定位到一个页面，在`View`标签中间多了空格，我使用的环境是`VS Code` ,虽然`VS Code`有自动保存的功能，但我使用任何工具都习惯默认状态，每次写完代码`Alt + Shift + F`，然后`CMD + S`, 但是对于View标签间没有子控件的情况，并不会帮我去空格，因此发生了这个BUG

  建议：这类没有内部子控件的标签，尽量使用自闭和标签, 如 ``` <View/> ```

- 用户授权

  当App中需要用到相机，访问一些传感器就会需要获取用户授权，在最初接到这个需求的时候，翻了翻RN的资料，然后发现了一个别人封装好的组件`react-native-permissions`,以前对`Android`适配稍微有点了解，刚好手上就有一台华为测试机，经测试效果还可以，正当我美滋滋的完成任务构建送测，测试那边反馈，两台小米都没有任何反应，开始排查问题...发现在小米手机上直接失败，考虑到国内手机厂商对`Android`的各种所谓‘定制’, `RN`默认提供的方式可能有些‘不接地气’，另外遇到的一个恶心的地方是`react-native-permissions`导致上`TestFlight`处理完一闪消失

  解决方案  
  `Android`稳妥起见，我先检查授权情况，如果授权失败了，自己再加个`Alert`，提示跳到系统设置页手动开启权限  
  `iOS`也是授权问题，当时没看这个库的源码，里面使用了很多我们未曾用到的权限代码，，在`plist`中全部加上即可

  缺点是  
  个别机型通过获取授权能够弹出提供授权选项，会遇到多次弹框，而之前发现有问题的小米机型上指挥收到一次自定义弹框。  
  为啥没有跳到对应App的权限设置页，因为我对`Android`不熟，搜出来了适配所有国内手机厂商的代码，看的我难受

- `2020-07-20` 好久没写过`RN`了, 换了设备随着上次升级`TypeScript`这次重新回到`RN`又一次升级了, 重新搭建`React-Native`环境,  因为`0.62`更换了`CocoaPods`管理`iOS`依赖库, 准备先把`Android`环境跑通, 首次执行`react-native run-android`遭遇滑铁卢, 情理之中, 意料之内.  

  在运行`react-native run-android`后报错如图:  
  ![run-android 报错.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/run-android%20报错.png?raw=true)  
  进入路径下对比发现:  
  ![此时路径下文件状态.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/此时路径下文件状态.png?raw=true)  
  分别去了上层目录下对比发现, 很有可能是文件没有下载完整导致, 反复执行`react-native run-android`都是没有意义的, 于是删除文件后, 重新执行`react-native run-android`等待下载安装依赖包  
  ![run-android.gif](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/run-android.gif?raw=true)  
  从终端的输出信息来看, 我的猜想是正确的, 最后对比了下文件正确的样子  
  ![该有的样子.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/该有的样子.png?raw=true)

- `2020-07-20` 安装`CocoaPods`依赖库失败, 因为项目使用到了`react-native-fast-image`, 这个库是基于`SD`的封装, `SD`又使用到了[libwebp](https://github.com/webmproject/libwebp), 在`CDN`加持下, 经过一系列依赖库的安装等待后, 迎来了`iOS`环境的首个报错:  

  ![run-ios.gif](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/run-ios.gif)
  ![libwebp安装失败大特写.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/libwebp安装失败大特写.png?raw=true)

  `Cloning into '/var/folders/6s/5hf9z6_138v68pqd7v891pd80000gn/T/d20200720-84015-8v2sj1'...
fatal: unable to access 'https://chromium.googlesource.com/webm/libwebp/': Failed to connect to chromium.googlesource.com port 443: Operation timed out`  

  通过域名和报错信息也能猜出来啥原因了, 暂时想到两个方案:
  1. 终端XXX (怕被简书再次疯, 这篇文章之前被封, 刚解封不久)  
  2. 修改源
  这里采取`方式2`, 修改源的方案

  ```bash
  1. 先确认下项目`react-native-fast-image`版本 ~> `8.1.5`  
  2. `react-native-fast-image`版本依赖的`SDWebImageWebPCoder `版本 ~> '~> 0.6.1'  
  3. 在本地`pod`源仓库中找到`libwebp`并修改源  
  默认源: `https://chromium.googlesource.com/webm/libwebp`  
  替换成: `https://github.com/webmproject/libwebp.git`  
   ```  

  ![SDWebImageWebPCoder依赖版本.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/SDWebImageWebPCoder依赖版本.png?raw=true)
  ![描述文件路径及默认源.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/描述文件路径及默认源.png?raw=true)
  换源保存后重新执行`pod install --verbose --no-repo-update`结果失败, 一看错误, 源地址没变, 如果你仔细看过, 在`描述文件路径及默认源`这张图中可以发现疑点, 我使用了`Trunk`源, 但是改的是`Master`中的源地址, 而我`podspec`也优先指定了`Trunk`源, 所以上一步换源无效, 重新修改对应的源地址后, 执行中验证确认修改成功
  ![这次才换源成功.png](https://github.com/ShenYj/ShenYj.github.io/blob/master/markdowns/React-Native/踩坑日记/assets/这次才换源成功.png?raw=true)
  到此完成`CocoaPods`依赖库安装
  
  `补充:`关于`libwebp`路径我已经在截图中展示了, 没有详细介绍, 可以根据我截图中的路径直接查找, 为了解决复杂的环境及场景, 可以通过`find path -iname libwebp`来查找路径:  
  
  e.g.

  ```bash
  shenyj@ShenYj-MBP Desktop % find >/Users/shenyj/.cocoapods/repos/trunk -iname libwebp  
  /Users/shenyj/.cocoapods/repos/trunk/Specs/1/9/2/libwebp
  ```
