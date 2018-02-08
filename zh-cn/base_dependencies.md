eros 项目中依赖只有2种，一种是 `JavaScript 依赖`，一种则是 `SDK 相关依赖`。

## JavaScript 依赖
`JavaScript 依赖`可以参考 package.json：

```js
"dependencies": {
	"eros-widget": "^1.0.0",
	"weex-ui": "^0.3.13beta2"
},
"devDependencies": {
	"babel-core": "~6.25.0",
	"babel-plugin-component": "^1.0.0",
	"babel-plugin-transform-runtime": "~6.23.0",
	"babel-preset-env": "~1.5.2",
	"babel-preset-stage-0": "^6.24.1",
	"babel-register": "^6.22.0",
	"eslint": "^4.17.0",
	"eslint-plugin-html": "^4.0.2",
	"eslint-plugin-vue": "^4.2.2"
}
```

在 `dependencies` 中我们配置了 `eros-widget` 和 `weex-ui`，`weex-ui` 我们知道是非常优秀的组件库，那么 `eros-widget` 是什么？

> `eros-widget` 是对我们 eros-sdk 拓展出来的 module 的二次封装，使我们直接在 `Vue 实例`使用，也就是 this 上调用，eros-widget 不是必须的，但他可以帮我们间接的稳定 eros-sdk，所以建议还是使用 widget 拓展的方法。

具体可以参考 [eros-widget 文档部分](/zh-cn/eros_widget)。

由于 weex 和 eros 的开发差异性， weex-ui 中的 demo 是需要进行小部分修改即可运行的，基本都是路径的问题，开发时可以自己看脚手架输入日志来解决。

## SDK 相关依赖
eros-sdk 中有：
* 拓展的 weex 不具有的 `module`。
* 拓展的 weex 不具有的 `component`。
* 拓展的`简易更新逻辑`。
* 内置了 `weex-sdk`。

> weex-sdk 每次在发布新版本的时候都会有些许的小问题，而不懂原生开发的同学是无法解决的，由 eros 来帮住这些开发者来进行 weex 的版本控制，是非常有必要的。

由此我们不难看出，保持 eros-widget 和 eros-sdk 最新，即可使用我们定期发布的新功能，而我们的更新方法也非常简单。

## 依赖更新
更新 `eros-widget`：

``` 
$ cnpm i eros-widget -S
```

更新 `eros-sdk`：

``` js
// iOS 开发
$ eros update ios

// Android 开发
$ eros update android
```

