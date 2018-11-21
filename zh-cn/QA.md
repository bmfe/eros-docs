## Q: 有办法把方法挂载到this上面吗？挂到vue实例上，全局那种，到每个组件都能直接this.xxx()调用。

如图![](https://i.loli.net/2018/11/21/5bf4f8a857021.png)

每个页面使用的时候直接 this.$test

## Q: ios 老版本升级（包名修改的那个版本）出现的问题解决方案
基础包名改版版本日志： https://bmfe.github.io/eros-docs/#/zh-cn/update_log_all?id=_20181011
>  问题1 如图

解决方案：若出现该位置报错，改为图中划线的代码即可

![](https://i.loli.net/2018/11/21/5bf4f8e4c8671.png)




> 问题2 如图
![](https://i.loli.net/2018/11/21/5bf4f9244e907.png)
若出现如上图报错的

解决方案：搜索BMBaseLibrary，这两处地方将其改为 ErosPluginBaseLibrary。然后重新run即可

如图
![](https://i.loli.net/2018/11/21/5bf4f924ae23a.png)


## Q: android 提交google play收到SSL Error Handler错误

因为 SSL 的验证 google 检查的更严一些，但是国内一般都不会做这个检测。

https://www.cnblogs.com/shoneworn/p/8182615.html

你可以参考这个 对 com.benmu.framework.activity.GlobalWebViewActivity 类 做一些修改吧。

> 在 GlobalWebViewActivity 120行代码开始修改成如下。

``` java
     @Override
        public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error) {
//            handler.proceed();
            final SslErrorHandler mHandler;
            mHandler = handler;
            AlertDialog.Builder builder = new AlertDialog.Builder(activity);
            builder.setMessage("ssl证书验证失败");
            builder.setPositiveButton("继续", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    mHandler.proceed();
                }
            });
            builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
                @Override
                public void onClick(DialogInterface dialog, int which) {
                    mHandler.cancel();
                }
            });
            builder.setOnKeyListener(new DialogInterface.OnKeyListener() {
                @Override
                public boolean onKey(DialogInterface dialog, int keyCode, KeyEvent event) {
                    if (event.getAction() == KeyEvent.ACTION_UP && keyCode == KeyEvent.KEYCODE_BACK) {
                        mHandler.cancel();
                        dialog.dismiss();
                        return true;
                    }
                    return false;
                }
            });
            AlertDialog dialog = builder.create();
            dialog.show();
        }
```
> 代码如果报错可能是因为你没有导包导致的，请对应impor 对应的包。

## Q: android 热更新 不替换bundle文件
> 热更新 并没有报错，文件和版本检查所有的地方都没有问题，就是没有替换新的 buindle 文件，更新并没有生效。

A: 
Android 增加了 APP 版本的检测。 您新包的 `eros.native.json` 里 version 版本如下：
```
'version': {
        'android': '1.0.0',
        'iOS': '1.0.0'
    }
```
不能高于 Android APP的版本，android 的版本 在 `/WeexFrameworkWrapper/app/build.gradle` 文件中
`versionName` 字段，默认是 1.0版本。

> 增加这个判断是为了防止一些可以做原生开发的同学 在某个版本中 对原生做了修改，然后发布更新包时会导致一些并没有更新Android APP 版本的用户发生错误。

## Q: Unable to resolve dependency for ':app@samplechannelDebug/compileClasspath': Could not resolve project :nexus. 

如图![](https://raw.githubusercontent.com/myliuyx/source/master/not_install_error.png)



A:  执行 `/platforms/android/WeexFrameworkWrapper` 目录下 `install.sh` 文件，等待完成 点击右上角 Try Again

> 如果执行 `install.sh` 了还是不行,请检查下文件里是否存在文件如果只有1个文件或文件夹可能下载过程出错了，把文件夹删除了重新执行下 `install.sh`

## Q: 热刷新不起作用（手动指定服务地址）

A:
 
热刷新不生效多半是因为你的电脑存在多个ip地址（虚拟网卡），导致 App 连接了错误的地址，可以通过在 `eros.native.js -> url` 中添加 `socketServer` 字段来指定热刷新服务地址来解决这个问题；<br>
同时也可以添加`jsServer`字段来指定访问的服务地址（即`eros dev`服务的地址）

```js
// eros.native.js 文件
'url': {
        ...
        // 在这里添加 socketServer 请将 ip 地址改为你电脑真实的地址，注意如果是 dhcp 分配的地址会发生变化
        'socketServer': 'ws://192.168.15.252:8890',
        // 指定访问服务地址
        'jsServer': "http://192.168.15.252"
		}
		...
```


## Q: 手机设置代理后不能上网

A:

1.首先请在查看一次`开发调试`的文档，确认一下是否打开了代理工具（charles、fiddler或其他），如果没有打开请打开相关代理工具；

- Charles 使用教程：http://www.jianshu.com/p/fdd7c681929c
- Fiddler 使用教程：http://www.jianshu.com/p/99b6b4cd273c

## Q: eros dev 开发服务不成功

A:
如果报错
```
...
webpackRun.run is not a function
...
```
请 cd 到您新建的项目中去在尝试

**mac/linux:**
正常来说 mac/linux 下不会有任何报错，如果提示权限不足请 sudo 执行。

**windows**
如果报权限不足，如果 dev.json 未做修改，默认是80端口，请找出80端口占用程序并关闭他。

如果报了以下错误:
> vue packages version mismatch

请全局安装 weex-toolkit，并运行：
```
weex repair
```

## Q: 为什么我当前打出来的 js bundle 会这么大

A:
目前脚手架中正在集成 tree-shaking，因为官方也并未使用 tree-shaking 来优化包体积，所以需要一定时间。

目前可以先这么优化：
```
// 当前：
import {buiButton} from 'Eros/bui'

// 改为：
import buiButton from 'Eros/bui/components/buiButton'
```

这样能明显减少包体积大小。

## Q: iOS资源下载慢，或者等待时间巨长（eros install ios 命令）

A:

执行 `eros install ios` 命令后会从 git 上加载iOS工程所需要的三方SDK，由于网络原因，或者是墙的原因一些资源加载巨慢，甚至会加载失败如下图：

![](./image/iosInstallError.jpeg)

**解决方法**:从 https://github.com/xiaohuapunk/WeexEros3Rd 仓库将里面的资源文件下载到本地，并拷贝到 (你的工程目录)/platforms/ios/WeexEros 中，然后再次执行`eros install ios`命令即可；

## Q: IOS模板工程无证书真机调试

A:

> 自 Xcode7 版本之后，苹果爸爸允许开发者无证书真机调试（跟有证书的还是存在一些差异的，很多功能都是用不了的，比如推送），简单几步就可以实现无证书真机调试；

##### 1.注册个人AppleID 账号

- 登录开发者后台（https://developer.apple.com/) 注册一个AppleID，如果之前有账号（就是下载app登录的那个账号）登录进去，同意一下条款即可；

- 在Xcode账号设置中，添加账号

![](./image/addAccounts.png)

##### 2.无证书真机调试需要先关闭 `Push Notifications` 功能；不然的话就会报下面的错误提示👇

> 我们的模板开启了推送功能，所以需要先将这功能关闭，个人账号是没有这功能的
<br/>

![](http://ww4.sinaimg.cn/large/0060lm7Tly1fmlbm99iibj30i40amjt1.jpg)

解决办法：

- 修改 Bundle Identifier 注：需要修改成别人没有用到过，苹果会验证唯一性

![](http://ww2.sinaimg.cn/large/0060lm7Tly1fmlbugnl9uj313w04m74s.jpg)

- 修改team，选择 None（需要先将team设置成none） 

![](http://ww3.sinaimg.cn/large/0060lm7Tly1fmlby2fkcnj313y0cuabw.jpg)

- 找到 Gapabilities，将 `Push Notitications` 关闭

![](http://ww2.sinaimg.cn/large/0060lm7Tly1fmlc0xwiorj31840cqtb6.jpg)

##### 3.在将 Team 设置为自己的账户即可

##### 升级工程文件
> 更新前记得将工程备份 <br>
> 有些更新需要更新工程文件，通过 `instal`更新不到，需要使用 `eros update` 命令；<br>

在终端输入 `eros update` 后按提示输入文件的路径即可更新

以下是每个文件路径
- install.sh: `/platforms/ios/WeexEros/install.sh`
- Info：`/platforms/ios/WeexEros/WeexEros/Info.plist`
- Podfile：`/platforms/ios/WeexEros/Podfile`
- AppDelegate.m: `/platforms/ios/WeexEros/WeexEros/AppDelegate.m` <br>
升级完后需要再次执行 `eros install ios`

##### 移除ATSDK
`Weex`默认集成了的 `ATSDK 性能监测工具`，`Eros` 中之前也集成了这个sdk，sdk中用到了私有api，会导致审核不通过，Eros 现在已经将其移除，如果你已经使用`Eros`开发了iOS app 下面是移除方法:

> 2017.12.22 之后初始化的工程不需要此操作

##### 1.升级Podfile文件

- Podfile：`eros update`，path 填写 `/platforms/ios/WeexEros/Podfile`。


##### 2.升级完后需要再次执行 `eros install ios`
##### 3.修改配置文件

> 在Xcode中：找到Other Linker Flags 并展开，在`Debug`和`Release`中，移除 `ATSDK`的引用，注意：需要将上面的 `-framework`也一同移除

![](./image/removeAtsdk.png)

##### 4.重新运行,成功运行起来则说明移除成功了

## Q: errCode->wx_network_error msg->Unable to resolve host 'app.weex-eros.com':No address associated with hostname 

A:
发生这个错误证明您的手机或者模拟器无法连接到您的服务，你需要检查以下情况排查为何无法连接到服务。
1. 确认服务是否开启，可以访问服务如：app.weex-eros.com:8889 （如果端口没有或者不一样需要对应修改）。
2. 如电脑可以正常访问，那么请使用手机浏览器访问继续查看。
3. 如果手机浏览器无法访问，那么您可能需要连接代理。
4. 如果设置代理以后还是无法访问，请确认代理是否设置成功，您可以尝试用手机访问下百度，是否可以访问，并且代理抓包成功。
5. 如果您不想设置代理，可以尝试在手机浏览器里直接用ip（如果有端口记得加上）访问您的服务，如果可以直接访问成功的话。
   您可以将eros-demo/config/eros.native.js 文件里 jsServer 字段改成您的 ip (如果有端口记得加上)。
   然后请重新pack运行 Android程序


## Q: Android 如何修改包名（同一个手机可以跑2个eros 项目）。

A: 修改gradle.properties 文件 APPLICATION_ID （gradle.properties 文件在项目的跟目录）。
   修改完了请记得 点击 sync now  同步下


## Q: Failed to resolve: com.android.support:appcompat
```
Failed to resolve: com.android.support:appcompat-v7:25.3.1 
Add Google Maven repository and sync project 
Show in File
Show in Project Structure dialog 
```
A： 这时候您可以直接点击 Add Google Maven repository and sync project 等待编译完成即可

## Q: 如何实现安卓的finish

A:
 
1.注册两个属性curHomeBackTriggerTimes: 1,
      maxHomeBackTriggerTimes: 2,//代表按几次退出APP。
2.实现方法
```
//安卓自定义退出APP
    androidFinishApp() {
      const globalEvent = weex.requireModule("globalEvent");
      globalEvent.addEventListener("homeBack", options => {
        this.curHomeBackTriggerTimes === this.maxHomeBackTriggerTimes &&
          this.$router.finish();

        this.$notice.toast({
          message: `点击返回${
            this.maxHomeBackTriggerTimes
          }次之后，会关闭应用，当前点击第${this.curHomeBackTriggerTimes}次`
        });
        this.curHomeBackTriggerTimes++;
      });
    }

```

3.在created中调用此方法

## Q: 导入项目后提示`android.enableAapt2 xxxxx`错误

A:
出现此类问题主要是AS在导入项目是用了新版本gradle编译插件造成的。eros官方目前推荐且默认使用的gradle版本为4.1，gradle plugin版本为3.0.0。如果你不小心升级了版本，可按照如下操作设置版本。

1.
![步骤1](https://bmfe.github.io/eros-docs/zh-cn/image/modify_gradle_verison.jpg)

2.点击右上角SynNow按钮同步项目。

## Q: 使用 weex-amui

A:
1. 安装 `weex-amui`
```shell
npm install --save weex-amui
```
2. 修改 `.babelrc` 文件， plugins => import 下添加：
```json
{
    "libraryName": "weex-amui",
    "libraryDirectory": "packages",
    "style": false
}
```
3. 使用
```
import { AmButton } from 'weex-amui'
```
## Q: bmchart添加热力图
 A:
 ### 申请百度地图AK
1、 访问并登录[百度地图开放平台][1]。

2、 创建应用。
![](https://ws1.sinaimg.cn/large/9290cd97gy1ftjvdx3wivj20p90a20ud.jpg)

3、 完善应用信息。
![](https://ws1.sinaimg.cn/large/9290cd97gy1ftjvgjv7enj20hm0grgoq.jpg)

4、按照要求提交后会自动生成应用及AK值。
![](https://ws1.sinaimg.cn/large/9290cd97gy1ftjvk6mc6mj209r0540t4.jpg)

![](https://ws1.sinaimg.cn/large/9290cd97gy1ftjvl3ibx4j20h207lab1.jpg)

### 下载所需[bmap.min.js][2]文件。
github: [echarts的地图扩展][3]

### 存放html及相应js文件

1、将eros内置的bm-chart.html及echarts.min.js文件拷贝，与下载的bmap.js一起放置到assets文件夹下，如下图所示
![](https://ws1.sinaimg.cn/large/9290cd97gy1ftzp2e4p5gj205u03lq31.jpg)

2、修改bm-chart.html文件，将其中的js路径修改为相对路径并添加百度地图的jssdk路径及bmap的路径，如下图所示
![](https://ws1.sinaimg.cn/large/9290cd97gy1ftzp40miwqj20ku034dgw.jpg)

### 数据样例格式
写法与[eros文档][4]所述一致，只需按照[echarts官网样例][5]配置参数即可。

![](https://ws1.sinaimg.cn/large/9290cd97gy1ftjwalxnf6j20cp0bmdgv.jpg)

[1]:http://lbsyun.baidu.com/ "百度地图开放平台"
[2]:https://github.com/apache/incubator-echarts/blob/master/dist/extension/bmap.min.js "bmap.min.js"
[3]:https://github.com/apache/incubator-echarts/tree/master/extension/bmap "echarts的地图扩展"
[4]:https://bmfe.github.io/eros-docs/#/zh-cn/eros_widget "eros文档"
[5]:http://echarts.baidu.com/examples/editor.html?c=heatmap-bmap "heatmap样例"

### bmchart引入bm-chart.html文件
    <bmchart scr='bmlocal://assets/heatmap/bm-chart.html' ref="chart" :options="$format(bmapInfo)" style="width:750; height:520;"  @finish='finish'></bmchart>
