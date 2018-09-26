> 执行此节之前，请确保`必须的环境`全都安装完成。

## 生成开发模板 
通过脚手架自动生成 eros 目录，并进入生成的项目目录下：

```bash
$ eros init
```
 依次填写:
 
* 项目名称，默认 `eros-demo`。
* 初始版本号，默认 `1.0.0`。
* 模板类型，默认 `带有官方示例的丰富项目结构`，另外一个是`纯净开发版`。
* 安卓包名，默认 `com.eros.demo`。

**注意：**安卓手机上不能出现 2 个包名相同的应用，如果有，需要卸载，为了同时安装多个 `eros app `设置请勿重复。

脚手架会自动从 github 上拉取最新的 [eros-template 开发模板](https://github.com/bmfe/eros-template)

然后进入开发目录，执行 `cnpm i` 加载`node_modules`
```
$ cd eros-demo
$ cnpm i
$ eros dev
```

## iOS 运行项目
确保您已经安装完成 [iOS 所需环境](/zh-cn/base_env)。

`cd`到iOS工程目录`platforms/ios/WeexEros` 执行`pod update`命令来拉取iOS工程的依赖
```
$ pod update
```

首次执行时间会稍长，命令执行完毕后找到当前目录下 `WeexEros.xcworkspace` 文件，双击即可唤起XCode打开 iOS 工程；<br>
然后在XCode选择相应的模拟器（比如iPhone 8），点击`▶`按钮来运行项目。


## Android 导入工程
确保您已经安装完成 [Android 所需环境](/zh-cn/base_env)。

* 命令行进入 `/platforms/android/WeexFrameworkWrapper/` 目录下 执行install.sh 文件。等待下载依赖库。

下载完成后手动导入：

1.点击 `AndroidStudio` 上方的` File---&gt;New---&gt;Import Project`。

![](https://img.benmu-health.com/gitbook/1505963461481.jpg)
2.找到 eros 在你本地的地址，选择 `platforms/android/WeexFrameworkWrapper`,点击`OK`。

![](https://img.benmu-health.com/gitbook/1505963624252.jpg)
3.待项目构建完成，点击 AndroidStudio 上方工具栏的 `Run`，即可运行项目。![](https://img.benmu-health.com/gitbook/1505963683163.jpg)

> 第一次打开 AndroidStuido 时，由于本地环境未配置好，AndroidStuido 会提示错误，按照 IDE 提示，点击 `sync` 同步一下，大部分环境问题都可以解决。

注：
* 可能您第一次构建的时间太长您也可以尝试[解决 Android Studio 第一次导入项目太慢](https://www.jianshu.com/p/ba8189146a6b)。实在不行就请耐心等待 Android Studio 自己构建完成吧
* 如果您并不能成功的运行起来,您可以尝试去后面看看[常见问题](/zh-cn/QA)。



## 模板目录结构

```bash
├── config                  
│   ├── eros.dev.js         // 脚手架相关配置
│   └── eros.native.js      // 客户端相关配置
├── dist                    // 静态资源生成目录
├── node_modules            // 依赖
├── scripts                 // 自动化脚本
├── platforms               // 平台基础代码
│   ├── android
│   └── ios
├── src                     // 开发路径
│    ├── assets             // 静态资源路径 一般存放图片
│    ├── iconfont           // 静态资源路径 一般存放 iconfont
│    ├── js                 // js bundle 开发路径
│    └── mock               // 本地请求 mock 地址
├── CHANGELOG.md            // 版本升级变动
├── README.md               // 首页
└── package.json            // npm 项目及依赖说明
```


首次打开我们已经为您内置了由一些 `demo`，您可以看到相关的页面，下面在开发之前还需要进行一些相关的配置和调试的学习。


> 如果在此节中出现任何问题均不能正常运行 demo，请在 [issue](https://github.com/bmfe/eros/issues?q=is%3Aissue+is%3Aclosed) 或者 [Q&A](https://bmfe.github.io/eros-docs/#/zh-cn/QA)中来寻到解决方法，如果还未找到，麻烦给我们[提issue](https://github.com/bmfe/eros/issues/new)。
