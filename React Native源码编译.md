# 编译环境
1. Android SDK RN要求的编译sdk版本最低为23 务必下载好对应版本
2. buildToolsVersion 23.0.2
3. minSdkVersion 16
4. ndk 版本r10e (我编译的源码版本是0.35.0-rc.0 ,r11b版本NDK 编译报错,这里NDK版本务必使用r10e版本)

#设置路径
1. local.properties 设置ndk.dir,sdk.dir
2. 安装react-native 我建议这个目录不要放在as的工程目录下,也可以复制之前下好的node_modules.由于不是所有人都要开发rn,所以我没有把rn相关的目录放在as的项目也目录下,这样要对android/settings.gradle 做一点修改。添加
    `include ':ReactAndroid'`
    `project(':ReactAndroid').projectDir = new File(rootProject.projectDir, '../RNpro/node_modules/react-native/ReactAndroid')`
    这样js可以可以用的单独的git管理,不影响android工程,便于android和ios协同开发。
3. 在app/build.gradle中添加依赖`compile project(':ReactAndroid')`,替换之前的预编译包的依赖
4. 在as中run你的工程,在node_modules所在路径下运行`npm start`。
#编译中遇到的问题
##查看`node_modules/react-native/ReactAndroid/build.gradle`文件,我们发现又很多的task,源码编译时会依次执行这些task,因为网络环境的问题,这些task可能会执行失败,我们可以根据这些task源码,配合错误log定位解决问题。下面给几种我遇到的编译错误。

1.`Error:Execution failed for task ':ReactAndroid:downloadBoost'.`
  `> java.io.FileNotFoundException: http://nchc.dl.sourceforge.net/project/boost/boost/1.57.0/boost_1_57_0.zip`
  手动下载boost_1_57_0.zip 复制到`/node_modules/react-native/ReactAndroid/build/downloads`目录下,下载不了请使用vpn。

2.`Error:Execution failed for task ':ReactAndroid:downloadJSCHeaders'.`
  `> java.net.UnknownHostException: svn.webkit.org`
   到https://svn.webkit.org/repository/webkit/!svn/bc/174650/trunk/Source/JavaScriptCore/API/ 下载`['JavaScript.h', 'JSBase.h', 'JSContextRef.h', 'JSObjectRef.h', 'JSRetainPtr.h', 'JSStringRef.h', 'JSValueRef.h', 'WebKitAvailability.h']`
   复制到`/node_modules/react-native/ReactAndroid/build/downloads/jsc`目录下没有文件夹自己创建。
   
3.`Error:Execution failed for task ':ReactAndroid:buildReactNdkLib'.`
   `> Process 'command '/Users/shixiang/Library/Android/android-ndk-r11b/ndk-build'' finished with non-zero exit value 2`
    这个是我遇到的ndk版本问题,使用android-ndk-r10e后正常。
    
4.修改源码需要重新make project,clean工程会重新下载所有的文件。

#参考文献
[1][Building React Native from source](http://facebook.github.io/react-native/releases/0.35/docs/android-building-from-source.html)