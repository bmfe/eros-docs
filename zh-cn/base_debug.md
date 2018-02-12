## iOS 模拟器调试


### 1. 开发服务

在项目根目录中起开发服务：

```
$ eros dev
```

脚手架会在项目根目录下生成 dist 文件，存放 JS Bundle。

### 2.关闭拦截器

![](https://img.benmu-health.com/gitbook/AppShow.gif)

当我们运行起模拟机的时候点，击屏幕上的调试按钮-&gt; 设置项 -&gt; Interceptor 关闭拦截器，然后点击刷新或者双击调试按钮刷新页面（安卓端需要重新进app），就会重新从服务器加载最新的 js 文件。

> 拦截器的主要作用就是控制加载 js 文件的方式，默认是打开，会从当前工程内置的资源中加载js文件（iOS就是从当前app的沙盒目录中加载），关闭拦截器后，就会从之前在 eros.native.js 中配置的 jsServer 服务器加载js资源文件；

这时候 eros 会去请求远端的 JS Bundle，请求路径为

> url.jsServer (eros.native.js) + /dist/js/pages/页面路径

注：

## Android 模拟器调试
使用 Android 模拟器调试需要设置模拟器代理指向起服务的电脑`(手动设置 wifi 代理，配置电脑 ip 即可)`。

重复 iOS 模拟器调试的步骤即可，**但需要注意，切换拦截器开关的时候，安卓端需要重新进app**。


> 注: 双击调试按钮即可快速刷新。

### 真机调试
1. 确保手机和电脑在同一 `wifi` 下。
2. 运行 `$ eros dev` 开发服务。
3. 手机插入电脑，在编辑器中选择目标为当前插入手机，运行，将 app 安装到手机中。 
4. 启动代理软件 `fidder `或者 `charles`，配置手机抓包。
5. 手动配置手机 `wifi 代理`，ip 为`电脑 ip`，端口为`代理端口`，一般默认 8888。
6. 如果拦截器开启，请关闭拦截器，`iOS 刷新即可，Android 重新进入应用`。

不出意外，就已经请求了电脑生成的 JS Bundle，这时候可以修改代码，刷新实时查看效果了。

### weex debug 真机断点调试

#### 断点之前

`如果您没有使用 eros-widget，可忽略，直接往下看`，由于 weex debug 中不能支持我们拼接公共代码注入到业务的 JS Bundle 中，所以会导致 widget 方法找不到，如果需要 debug 调试 目前暂时的方法是在 debug 页面添加。

```js
if (process.env.NODE_ENV === 'development') require('Config') // 添加 appboard 路径即可 默认 Config

export default {
   ...
}
```
> 这样会增加 eros dev 生成的包体积，但最终在 build 的时候是不会打包进去的，但会使 eros dev 的时候，声明周期执行两次，打到内置包中便不会出现这样的情况，后续会优化，请放心，不影响程序运行。

#### 开始调试

1. 重复上面真机调试的所有步骤。
2. 执行命令：`$ weex debug`，执行完毕后会自动唤起 Chrome 浏览器打开调试主页，并展示出了二维码。
3. 在App中，点击屏幕上的调试按钮在弹出框中点击`调试`，呼出扫一扫，对准二维码。
4. 选择 Debugger 即可断点调试(这时候页面是空白的，只能调试逻辑代码)，点击 Inspector 即可查看页面结构。


