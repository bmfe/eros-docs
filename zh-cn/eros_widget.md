# 下载使用

> `widget` 是前端基于客户端拓展出来的 modules 相关功能的二次封装，以vue插件的形式，通过 appboard 全部注入到客户端的 framework Vue 原型上，我们直接通过 vue 中的 this 来快速使用。

我们已经封装好提交到了 npm 上，根据文档您已经执行完 `cnpm/npm install` 了，已经下载到了本地，并且已经在 appboard JS Bundle (默认 config/index) 中注入了配置。


# 页面全局事件

在不影响 vue 生命周期的前提下，eros 基于 `globalEvent` 暴露出了额外的事件供您使用。

**结合 widget 使用方法：**

```js
export default {
    created() {},  // vue 生命周期
    eros: {
        // app 前后台相关 start 
        appActive() {
            console.log('appActive');
        },
        appDeactive() {
            console.log('appDeactive');
        },
        // app 前后台相关 end 

        // 页面周期相关 start 
        beforeAppear (params, options) {
            console.log('beforeAppear');
        },
        beforeBackAppear (params, options) {
            console.log('beforeBackAppear');
        },
        appeared (params, options) {
            console.log('appeared');
        },

        backAppeared (params, options) {
            console.log('backAppeared');
        },
        beforeDisappear (options) {
            console.log('beforeDisappear');
        },
        disappeared (options) {
            console.log('disappeared');
        },
        // 页面周期相关 end 

        // 局部个推通知 
        pushMessage (options) {
            console.log('pushMessage');
        }
    }    
}
```

**注意事项：**

1. 这些钩子函数都是**异步**的。
 
2. 若用到`<embed>`标签，`<embed>`中的内容是**不会触发**全局事件的。

**Api：**
- **appActive( )**
    
    触发时机：App从【后台】切换至【前台】时触发。

- **appDeactive( )**

    触发时机：App从【前台】切换至【后台】时触发。

- **beforeAppear( params, options )**

    触发时机：首次进入本页面，在本页面【即将出现】时触发

    params：`$router.open` 时传递的 params
    
    options：目前仅包括属性`type`，值为`open`、`refresh`，前端同学暂时用不到

- **appeared( params, options )**

    触发时机：首次进入本页面，在本页面【已经出现】时触发

    params：`$router.open` 时传递的 params
    
    options：目前仅包括属性`type`，值为`open`、`refresh`，前端同学暂时用不到

- **beforeBackAppear( params, options )**

    触发时机：从其他页面返回本页面，在本页面【即将出现】时触发，比如切换原生Tabbar页面，使用`$router.back`返回当前页面时

    params：`$router.setBackParams` 时设置的 params
    
    options：目前仅包括属性`type`，值为`back`，前端同学暂时用不到

- **backAppeared( params, options )**

    触发时机：从其他页面返回本页面，在本页面【已经出现】时触发，比如切换原生Tabbar页面，使用`$router.back`返回当前页面时

    params：`$router.setBackParams` 时设置的 params
    
    options：目前仅包括属性`type`，值为`back`，前端同学暂时用不到

- **beforeDisappear(options)**

    触发时机：在本页面【即将消失】时触发
    
    options：目前仅包括属性`type`，值为`open`、`refresh`、`back`，前端同学暂时用不到

- **disappeared(options)**

    触发时机：在本页面【已经消失】时触发
    
    options：目前仅包括属性`type`，值为`open`、`refresh`、`back`，前端同学暂时用不到

- **pushMessage(options)**

    触发时机：收到个推发送信息且在使用页面时触发
    
    options：推送信息

# $axios（请求）

**注意：App中后端返回的数据格式通常为`json`，所以请确保你们后端 response header 中content-type：值为 application/json，不然可能无法正常解析数据**

**配置请求参数编辑 config/index.js**

```js
ajax: {
        baseUrl: 'http://app.weex-eros.com:52077',
        /**
         * 接口别名
         */
        apis,
        // 接口超时时间
        timeout: 30000,

        /**
         * 请求发送统一拦截器 （可选）
         * options 你请求传入的所有参数和配置
         * next
         */
        requestHandler (options, next) {
            console.log('request-opts', options)
            next()
        },
        /**
         * 请求返回统一拦截器 （可选）
         * options 你请求传入的所有参数和配置
         * resData 服务器端返回的所有数据
         * resolve 请求成功请resolve你得结果，这样请求的.then中的成功回调就能拿到你resolve的数据
         * reject 请求成功请resolve你得结果，这样请求的.then中的失败回调就能拿到你reject的数据
         */
        responseHandler (options, resData, resolve, reject) {
            const { status, errorMsg, data } = resData
            if (status !== 200) {
                console.log(`invoke error status: ${status}`)
                console.log(`invoke error message: ${errorMsg}`)
                reject(resData)
            } else {
                // 自定义请求逻辑
                resolve(data)
            }
        }
    }
...

```

**发送请求**`$fetch(info)`

示例：

```js
// 示例
this.$fetch({
    method: 'GET',    // 大写
    name: 'common.getInfo', //当前是在apis中配置的别名，你也可以直接绝对路径请求 如：url:http://xx.xx.com/xxx/xxx
    data: {
        count: 1
    }
}).then(resData => {
    // 成功回调
    console.log(resData)
}, error => {
    // 错误回调
    console.log(error)
})
```

Api：

* **method（String）**：请求方式，分为`GET、POST、HEAD、PUT、DELETE、PATCH`。`(必须大写)`
* **name（String）**：请求地址，如果已经在config/apis.js下配置了接口的请求别名，则可以直接调用别名。
* **url（String）**: 如果你不想配置别名，可以直接输入相对路径或者绝对路径来请求。
* **data（Object）**：请求携带的参数。
* **params(Object) **: 请求路径的动态参数，如url:api/product/{productId},则params:{productId:...}。
* **header（Object）**：当前请求的请求头设置。
* **then（Promise）**：`Promise`接口返回时候会触发。

# $router（路由）

### src/js/config/routes.js

路由配置

我们在 routes.js 中配置页面对应 js 的别名，和导航条显示文案。

> **主页的导航条默认是隐藏的，如果需要，便在全局事件 eros.beforeAppear 中调用 this.$nav.setNavigationInfo 来自行设置。**

```js
// src/js/config/routes.js
export default {
    // 首页
    'home': {
        url: '/pages/home/index.js',
    },
    'router.test': {
        // 不设置title自动隐藏导航
        title: '路由测试页面',
        url: '/pages/home/tab2/components/router/index.js',
    },
    'demo': {
        url: '/pages/demo/index.js',
        title: '新闻'
    }
}
```

Api：

* **url（String）**：页面相对 js 地址，从 pages/ 下写即可。
* **title（String）**：页面导航显示文案，默认不传或为空则自动隐藏导航条。


### $router API：

* **$router.open**：跳转到一个页面。
* **$router.back**：返回到之前打开的某个页面。
* **$router.getParams**：获得open时候传递的参数。
* **$router.refresh**：重载当前weex实例。
* **$router.setBackParams**：为返回的页面传递参数。
* **$router.toWebView**：跳转一个webView页面。
* **$router.setHomePage**:重新设置启动的首页，并立即跳转页面。
---

### $router.open

跳转到一个页面。

示例：

```js
this.$router.open({
    name: 'demo',
    type: 'PUSH'
})
```

Api：

* **name（String）**：跳转页面别名，在 **config/pages.js自行配置。**
* **type（String）**：页面打开方式，默认是`PUSH`。
  * `PUSH`右侧打开。
  * `PRESENT`底部弹出。
* **params（Object）**：需要传递的参数。
* **canBack（Boolean）**：目标页面是否可以返回。
* **gesBack（Boolean）**：目标页面是否开启手势返回。默认开启仅支持iOS；
* **navShow（Boolean）**：是否显示导航条。
* **navTitle（String）**：导航条文案。
* **statusBarStyle（String）**：状态栏\(电量，信号\)样式。
  * `Default`黑色。
  * `LightContent` 白色。
* **backCallback（Function）**：页面返回时的回调。
* **backgroundColor（String）**：原生页面背景颜色（可以通过设置页面背景色，感官上页面不会白屏）。

注意：
**backCallback方法触发时，会禁用返回功能。**

### **$router.back**

返回之前的路由页面。

示例：

```js
this.$router.back({
    length: 2,
    type: 'PUSH',
    callback() {
        // 返回成功回调
    }
})
```

Api：

* **length（Number）**：返回之前的第几个页面，默认是 `1`**。**
* **type（String）**：页面打开方式，默认是`PUSH`。

  * `PUSH`右侧打开。
  * `PRESENT`底部弹出。

* **callback（Function）**：页面返回时的回调。

> 注：如果`ios`端是用`PRESENT`打开的页面，返回这个页面也需要传`PRESENT`，如果你在打开一个`PRESENT`页面之后又打开了几个`PUSH`类型的页面，传入`PRESENT`会把这些页面都出栈。

### $router.getParams 

获取传递的参数。

示例：

```js
this.$router.getParams().then(resData => {
    console.log(resData)
})
```

resData.data 是你在上一个页面中调用 $router.open 跳转传递的 params 参数。

### $router.refresh 

重新加载并渲染当前 js ，并重新走生命周期。

示例：

```js
this.$router.refresh()
```

### $router.setBackParams 

设置返回页面的参数。

示例：

```js
this.$router.setBackParams({
   name: 'weex-eros'
})

// 目标页面
export default {
    eros: {
        beforeBackAppear(params) {
            console.log(params)
        }
    }
}
```

### $router.toWebView 

打开app内置webview页面

示例：

```js
this.$router.toWebView({
    url: '',                                    // 页面 url 支持打开本地的html文件 比如：bmlocal://assets/test.html
    title: '',                                  // 页面 title
    navShow: true                               // 是否显示native端导航栏，默认是true
})
```
**扩展功能：**
> native 端在 webview 运行环境注入了一个 bmnative 对象，用于 js 与 Native 端进行交互，js 可以直接调用 bmnative 对象的方法

1. closePage() 方法：关闭webview页面返回上一个页面；
```js
// 示例
bmnative.closePage()
```

2. fireEvent('event','info') 触发 weex 通过 [bmEvent](https://bmfe.github.io/eros-docs/#/zh-cn/eros_widget?id=event%EF%BC%88%E5%8F%91%E5%B8%83%E8%AE%A2%E9%98%85%EF%BC%89) 注册的事件，用于web与weex交互，'info'目前只支持字符串传递；
```js
// 示例
bmnative.fireEvent('event', 'info');
```

	示例：[test.html](https://github.com/bmfe/eros/blob/master/src/assets/test.html)

### $router.openBrowser 

使用系统浏览器打开url

示例：

```js
this.$router.openBrowser(url)
```

Api：

* **url（String）**：需要打开的url地址。

### $router.setHomePage 

设置App启动首页 

示例：

```js
this.$router.setHomePage(path)
```

Api：

* **path（String）**：需要设为首页的path路径。

 > native端会将传过来的path存储到本地，app启动的时候优先读取这个参数来显示首页，没有的话读取 native.js 中填写的 homePage <br>
 注意：path 路径格式需要与 homePage 一致从 `/pages/...`开始

 ```js
// 示例
// 首页为登录页面
this.$router.setHomePage('/pages/eros-demos/login/index.js')
// 登录成功后切换到home页面
this.$router.setHomePage('/pages/eros-demos/home/index.js')
```

# $storage（本地化存储）

我们在 app 端**异步的存储**一些数据信息，并已经为您封装并返回`Promise`。

注意：**只支持存字符串 或者 json对象，widget 会自动把数字变为字符串存储**

#### set
> 异步设置存储信息。

示例：

```js
this.$storage.set('name', 'weex-eros')
.then(resData => {}, error => {})

this.$storage.set('info', {
    name: 'weex-eors'
})
.then(resData => {}, error => {})
```
#### setSync
> 同步设置存储信息。

示例：

```js
this.$storage.setSync('name', 'weex-eros')
```

#### get

> 异步获取存储信息。

示例：

```js
this.$storage.get('name').then(resData => {
    console.log(resData) // weex-eros
})
```
#### getSync
> 同步获取存储信息。

示例：

```js
var result = this.$storage.getSync('name')
```

#### delete

> 异步删除一条信息。

示例：

```js
this.$storage.delete('name').then(resData => {
    console.log("删除成功") 
})
```
#### deleteSync
> 同步删除一条信息。

示例：

```js
var result = this.$storage.deleteSync('name')
```

#### removeAll

> 异步删除所有本地信息。

示例：

```js
this.$storage.removeAll().then(resData => {
    console.log('本地所有持久化存储的数据都已被清空。')
})
```
#### removeAllSync
> 同步删除所有本地信息。

示例：

```js
var result = this.$storage.removeAllSync('name')
```





# $coms（联系相关）

打电话，发短信，打开通讯录等。

#### call

> 打电话。（仅真机）

示例：

```js
this.$coms.call('10086')
```

##### Arguments
* to `(string)` 电话号码
* tip `(boolean)`  `(default: true)` `only Android` 是否显示拨打弹窗

#### sms

> 发短信。（仅真机）

示例：

```js
this.$coms.sms(['110', '120', '119'], '给你发送一条消息').then(data => {
    console.log("发送成功")
}, error => {
    console.log("发送失败", error)
})
```

#### contacts

> 打开联系人列表。

示例：

```js
this.$coms.contacts().then(data => {
    console.log("获取联系人信息成功", data)
}, error => {
    console.log("获取失败", error)
})
```

# $event（发布订阅）

> 与 weex 拓展的 globalEvent 所不同，event 主要做的是不同页面 js 之间的通讯，和 globalEvent 主要负责客户端和 js 的通讯。

举个例子，假如你有 2 个页面， A.js 和 B.js，以下简称 A B：

你在 A 中注册一个事件，并设置其回调

```
this.$event.on('AeventName', params => {})
```

这样你便可以在 B 中触发这个事件

```
this.$event.emit('AeventName')
```

当然你也可以在 A 中在注册事件之后来调用这个事件

**需要注意的是，如果你在 B 中注册，从 B 返回 A，B的实例会被销毁，事件自然也会被销毁**。

而细心的你可能会注意到，我们在项目中有个 pages/mediator 文件夹，下面有个 index.js ，他是干什么的呢？

> **mediator/index.js 会在 app 启动的时候，作为第一个 js bundle 被执行，并且常驻内存，可以在bus上绑定事件，保存数据和状态，可以做类似 sessionStore 或者 Vuex的功能，当然这些数据，状态和事件都会随着app关闭而销毁，如果需要本地持久化存储，就搭配着 this.$storage 来使用即可。（因为常驻内存，请不要在这里面进行过量存储，时不时看下你的内存使用状态）**

#### on

> 注册事件，可被响应多次。

示例：

```js
this.$event.on('eventName',(params) => {
    // params 为触发该事件所传的参数
});
```

#### once

> 注册事件，只响应一次。

示例：

```js
this.$event.once('eventName',(params) => {
    // params 为触发该事件所传的参数
});
```

#### emit

> 触发事件。

示例：

```js
this.$event.emit('eventName',params)
```

#### off

> 解绑事件。

示例：

```js
this.$event.off('eventName',(result) => {
    console.log("取消该事件");
});
```

#### offall

> 解绑全部事件。

示例：

```js
this.$event.offall();
```

# $font（字体相关）

* **changeFontSize** 改变字体大小。

* **getFontSize** 获取当前字体大小。

---

#### changeFontSize

> 改变字体大小。

示例：

```js
this.$font.changeFontSize({
    fontSize: 'NORM'
}).then(resData => {
    // 收起成功的回调
}, error => {})
```

Api:

* **fontSize**（**String**）：字体大小
  * NORM：标准。
  * BIG：大。
  * EXTRALARGE：特大。

#### getFontSize

> 获取当前字体大小。

示例：

```js
this.$font.getFontSize().then(resData => {
    console.log(resData)
})
```

# $format (echart 相关)
> 使用过 echart 的同学都知道，我们在传递 options 配置的时候，可以传入方法，但 weex 内部的机制，当我们传递带有方法的 options 到底层 sdk 的时候，便被自动 JSON.stringify 了一下，所有含有 function 的属性全部都会被过滤掉，为此我们在全局提供了一个 **$format** 方法，来帮助我们传递参数，使用起来也非常简单，直接上 eros-demo 中的实例。

```js
   <bmchart ref="chart" :options="$format(options)" style="width:750; height:520;"  @finish='finish'></bmchart>
```

只要在使用的时候加个 $format 就行。

如果您想还原 echart 原本的开发感觉，自然可以把我们的 bmchart 组件在包装成一个组件，使用即可。

这样以来，eros 便能运行绝大部分 echart 的实例( 前提是手机 hold 的住 )。



# $geo（当前位置信息）

#### get

> 获取当前位置坐标（GCJ-02 火星坐标系，高德，腾讯，Google中国所使用）。

**注意**：Android 平台的定位是基于高德地图SDK实现的，所以需要集成高德地图并调用初始化方法后[集成方法](https://bmfe.github.io/eros-docs/#/zh-cn/plugin_amap)，才可以使用此api获取位置信息，iOS平台可以直接调用，不需要集成；

示例：

```js
this.$geo.get().then(data => {
    console.log(data)
}, error => {
    console.log(error)
})
```

# $image（图片相关）
---
#### pickAndUpload

> 选择本地图片并上传图片。

**项目根目录下 eros.native.js 中 url.image 图片上传的地址为默认上传地址，根据需求来填写。**

**提示：App上传图片的时候请求的 content-type 值是 image/jpeg 或 image/png，Content-Disposition：中 name 字段值是 file，请后端同学解析的时候注意一下；**

示例：

```js
this.$image.pickAndUpload({
    url: '',                     // 自定义图片上传地址，默认上传地址是 eros.native.js 中的 image 地址
    maxCount: 9,                 // 一次最多可选择图片数量 最大支持9张 默认是1
    imageWidth: 1000,            // 图片宽度（根据宽度按比例裁剪）可以不传默认为 800px
    allowCrop: true,             // 是否允许编辑（只有当 maxCount == 1时 此参数才会生效） 默认是false
    params: {},                  // 传递的参数
    header: {}                   // 自定义请求 header
})
.then(resData => {
    console.log(resData)    // [url1, url2...]
}, error => {
    console.log(error)
})
```

参数：
* **url**（**String**）：自定义图片上传地址，默认上传地址是 eros.native.js 中的 image 地址。
* **maxCount**（**Number**）：一次最多可选择图片数量 最大支持`9`张 默认是`1`。
* **imageWidth**（**Number**）：图片宽度（根据宽度按比例裁剪）可以不传默认为 `800px` 默认是`0`。
* **allowCrop**（**Boolean**）：是否允许编辑（只有当 `maxCount == 1`时 此参数才会生效） 默认是 `false`。
* **params**（**Object**）：上传图片的时候传递的参数。
* **header**（**Object**）：上传图片自定义请求 header，可以放 token 之类的值。
---

#### upload

> 上传图片

**项目根目录下 eros.native.js 中 url.image 图片上传的地址为默认上传地址。**

示例：

```js
this.$image.upload({
    url: '',                     // 自定义图片上传地址，默认上传地址是 eros.native.js 中的 image 地址
    params: {},                  // 传递的参数
    header: {},                  // 自定义请求 header
    source: []                   // 图片路径
})
.then(resData => {
    console.log(resData)    // [url1, url2...]
}, error => {
    console.log(error)
})
```

参数：
* **url**（**String**）：自定义图片上传地址，默认上传地址是 eros.native.js 中的 image 地址。
* **params**（**Object**）：删除图片的时候传递的参数。
* **header**（**Object**）：上传图片自定义请求 header，可以放 token 之类的值。
* **source**（**Array[String]**）：图片的路径，本地图片通过`pick`可以获取到本地路径。
---
#### camera

> 拍照并获取图片地址，**获取的本地地址可直接在 image 标签的 src 属性上使用**。


示例：

```js
this.$image.camera({
    imageWidth: '800',                  
    allowCrop: true                  
})
.then(resData => {
    console.log(resData)   
}, error => {
    console.log(error)
})
```

参数：
* **imageWidth**（**Number**）：图片宽度（根据宽度按比例裁剪）可以不传默认为 `800px`。
* **allowCrop**（**Boolean**）：是否允许编辑，默认是 `false`。
---
#### pick

> 选取本地图片并处理，**获取的本地地址可直接在 image 标签的 src 属性上使用**。


示例：

```js
this.$image.pick({
    maxCount: 9,
    imageWidth: '800',                  
    allowCrop: true                  
})
.then(resData => {
    console.log(resData)   
}, error => {
    console.log(error)
})
```

参数：
* **maxCount**（**Number**）：一次最多可选择图片数量 最大支持`9`张 默认是`1`。
* **imageWidth**（**Number**）：图片宽度（根据宽度按比例裁剪）可以不传默认为 `800px` 默认是`0`。
* **allowCrop**（**Boolean**）：是否允许编辑（只有当 `maxCount == 1`时 此参数才会生效） 默认是 `false`。
---

#### preview 

> 调用图片浏览器，浏览本地或者远端图片，输入本地地址或者远端地址即可。

示例：

```js
this.$image.preview({
    index: 2,                     // 所点击图片下标
    images: ['1','2','3'],        // 图片的网络地址
})
```

参数：

* **index**（**Number**）：所点击图片下标。
* **images**（**Array**）：图片的网络地址集合。
---


#### borwser （下个大版本会抛弃，请使用 `preview`）

> 调用图片浏览器，只能浏览远端图片。

示例：

```js
this.$image.browser({
    index: 2,                     // 所点击图片下标
    images: ['1','2','3'],        // 图片的网络地址
    type: 'network'               // 图片的来源,目前只支持网络图片 
})
```

参数：

* **index**（**Number**）：所点击图片下标。
* **images**（**Array**）：图片的网络地址集合。
* **type**（**String**）：图片的来源,目前只支持网络图片。


# $navigator（原生导航条相关操作）

> 设置原生导航栏按钮，支持文字，或图片

### 图片尺寸及命名规定
> size: 请以 iPhone6（750x1334) 的尺寸设计，并切图； <br>
命名：请将图片命名成 xxx@2x.png(注意 x 是小写的)，因为iOS系统会根据图片命名设置图片的size，对安卓不影响； <br>
注：图片不要使用中文命名

#### setNavigationInfo

设置导航条相关信息。

示例：

```js
this.$navigator.setNavigationInfo({
    title: '',                              // 中间区域标题    
    navShow: true,                          // 是否显示导航栏
    statusBarStyle: 'Default'               // 状态栏文字颜色：Defautl 黑色， LightContent 白色
}, () => {
     // 点击回调
    console.log("您点击了导航条中间区域。")
})
```
> Android 版本在4.4 以上的小米、魅族可以设置statusBarStyle 状态栏文字颜色，其他安卓手机必须在6.0以上才会生效！

#### setLeftItem

设置导航条左侧区域相关属性和方法。

示例：

```js
this.$navigator.setLeftItem({
    text: '',                               // 展示的文字 (和图片 二选一)    
    textColor: '',                          // 文字颜色 (默认为白色)
    fontSize: '32',                         // 字号（默认32px）
    fontWeight: 'normal',                   // 是否加粗（默认不加粗）
    image: ''                               // 展示的图片url (和文字 二选一，文字优先级更高)
}, () => {
     // 点击回调
    console.log("您点击了导航条左侧区域。")
})
```

#### setRightItem

设置导航条右侧区域相关属性和方法。

示例：

```js
this.$navigator.setRightItem({
    text: '',                               // 展示的文字 (和图片 二选一)    
    textColor: '',                          // 文字颜色 (默认为白色)
    fontSize: '32',                         // 字号（默认32px）
    fontWeight: 'normal',                   // 是否加粗（默认不加粗）
    image: ''                               // 展示的图片url (和文字 二选一，文字优先级更高)
}, () => {
     // 点击回调
    console.log("您点击了导航条右侧区域。")
})
```

#### setCenterItem

设置导航条中间区域相关属性和方法。

示例：

```js
this.$navigator.setCenterItem({
    text: '',                               // 展示的文字 (和图片 二选一)    
    textColor: '',                          // 文字颜色 (默认为白色)
    fontSize: '32',                         // 字号（默认32px）
    fontWeight: 'normal',                   // 是否加粗（默认不加粗）
    image: ''                               // 展示的图片url (和文字 二选一，文字优先级更高)
}, () => {
     // 点击回调
    console.log("您点击了导航条中间区域。")
})
```

# $notice（通知）

#### **alert 单按钮弹窗**

示例：

```js
this.$notice.alert({
    title: '这是一个弹窗',
    message: '消息',
    okTitle: '确认',
    callback() {
        // 点击确认按钮的回调
    }
})
```

Api：

* **title（String）**：弹窗标题。
* **message（String）**：弹窗信息。
* **okTitle（String）**：弹窗按钮文案。
* **callback（Function）**：弹窗点击按钮回调。

#### **confirm 双按钮弹窗**

示例：

```js
this.$notice.confirm({
    title: '这是一个弹窗',
    message: '消息',
    okTitle: '确认',
    cancelTitle: '取消',
    okCallback() {
        // 点击确认按钮的回调
    },
    cancelCallback() {
        // 点击取消按钮的回调
    }

})
```

Api：

* **title（String）**：弹窗标题。**\(选填\)**
* **message（String）**：弹窗信息。**\(选填\)**
* **okTitle（String）**：弹窗按钮文案。**\(选填\)**
* **cancelTitle（String）**：取消按钮文案。**\(选填\)**
* **okCallback（Function）**：弹窗点击确认按钮回调。**\(选填\)**
* **cancelCallback（Function）**：弹窗点击取消按钮回调。**\(选填\)**

#### loading 加载提示

示例：

```js
// 显示loading
this.$notice.loading.show("loading展示文案")

// 隐藏loading
this.$notice.loading.hide()
```

Api：

* **show（调用）**：显示 loading。**\(方法\)**
* **hide（调用）**：隐藏 loading。**\(方法\)**

#### toast 吐丝提示

示例：

```js
this.$notice.toast({
    message: '消息'
})
```

Api：

* **message（String）**：吐丝信息。**\(必填\)**


# $tools (工具相关)

### Api

* **resignKeyboard** 收起键盘。
* **copyString** 复制内容到剪切板。
* **scan** 扫一扫。
* **networkStatus** 获取网络状态。
* **watchNetworkStatus** 监听网络状态。
* **clearWatchNetworkStatus** 取消监听网络状态。

### resignKeyboard

> 收起键盘。

示例：

```js
this.$tools.resignKeyboard().then(resData => {
    // 收起成功的回调
}, error => {})
```


### copyString

> 复制内容到剪切板。

示例：

```js
this.$tools.copyString("weex-eros").then(resData => {
    // 复制成功的回调
}, error => {})
```

### scan

> 调起 app 扫一扫。

示例：

```js
this.$tools.scan().then((resData) => {
    console.log(resData)
}, error => {})
```

### networkStatus

> 获取网络状态

```js

const status = this.$tools.networkStatus()
```
网络状态 `status`：
- `UNKNOWN` 未知
- `NOT_REACHABLE` 不可达（无网络）
- `WIFI` 无线网
- `3G/4G` 手机移动网络

### watchNetworkStatus

> 监听网络状态

```js
this.$tools.watchNetworkStatus().then((status) => {
    // 网络状态 status：
    // UNKNOWN 未知
    // NOT_REACHABLE 不可达（无网络）
    // WIFI 无线网
    // 3G/4G 手机移动网络
}, err => {
    // 监听失败 错误回调 status , errorMessage
})
```

### clearWatchNetworkStatus

> 取消监听网络状态

```js
this.$tools.clearWatchNetworkStatus()
```


# 拓展

> 当我们暴露出来的widget api 已经无法满足您时，你可以便可以自行基于客户端暴露出来的 modules 来拓展自己的 widget 。

假如我们要添加一个基于 bmModal module 创建的 widget 。

首先添加 **widget/toast.js** 。

![](https://img.benmu-health.com/gitbook/widgetToast.png)

**toast.js** 中用`vue插件`的写法来写。

```js
var modal = weex.requireModule('bmModal'),
    Toast = Object.create(null)

Toast.install = (Vue, options) => {
    Vue.prototype.$toast = (options) => {
        if(!options.message) return
        modal.toast({
            message: options.message,
            duration: options.duration || 2000
        })
    }
}

Vue.use(Toast)
```
在 `Config/index` 导入一下(需在 eros.dev.js 配置 alias 别名 Widget)。
```
import 'Widget/toast'
```
toast widget 已经大功告成，使用一下。

```js
this.$toast({
    message: '我自己拓展的',
    duration: 200
})
```
