## bindingx 由来

引用官方文档上的介绍：

* 由于 **weex 底层使用的 JS-Native Bridge 具有天然的异步特性**，这使得 JS 和 Native 之间的通信会有固定的性能损耗，因此在一些复杂的交互场景中，JS 代码很难以高帧率运行。举个例子，如果我们要实现 `视图随手势移动` 的效果，那么按照传统的方式，需要在这个视图上绑定 `touch` 或者 `pan` 事件，当手势发生时， Native 会将手势事件通过 Bridge 传递给 JS , 这产生了一次 Native 到 JS 的通信。而 JS 在接收到事件后，需要根据手指移动的偏移量驱动界面变化，这又会产生一次 JS 到 Native 的通信。**与此同时，手势回调事件触发的频率是非常高的，频繁的通信带来的时间成本很可能导致界面无法在16ms中完成绘制，进而产生卡顿**。

* 事实上，不仅仅是在 weex 上存在这种问题, React Native 等框架同样存在类似的问题。拿 React Native Animated 组件为例，为了实现流畅的动画效果，这个组件采用了声明式的API，在 JS 端仅仅定义了输入与输出以及具体的 transform 行为，而真正的动画是通过 Native Driver 在 Native 层执行，这样就避免了频繁的通信。然而，这个方案只能解决一部分问题，如果是有复杂交互操作的场景就不够用了。另外，声明式的方式能够定义的行为非常有限，无法满足更复杂的交互场景。

## 使用之前
请熟读官方文档：

* [bindingx 文档](https://alibaba.github.io/bindingx/guide/cn_introduce)


`weex native` 项目的使用过程中需要注意：

1. 传入的元素需要多取一层 `ref` 属性，假设我们给一个元素上面 `ref` 属性赋值为 `box` ，则使用时候按照以下方式才可：

```js
this.$refs.box.ref
```

2. 如果引用方式为 `requireModule('bindingx')` 这种 weex 引入 module 的写法，bind 方法按照文档中的方式是无效的，需要做特殊处理，后面会说到。

3. ios 端引入的时候需要做下页面手势返回处理 (eros 中拓展了`$router gesBack` 属性)，防止右滑时候出现`手势冲突`。

## eros 中使用

如果下载官方的 npm 包 `weex-bindingx` 直接使用时没有问题的，但 eros 是主要 focus native，引入 `weex-bindingx` 在打出来一个简单的 JS Bundle 就要 `190+ kb`，很明显有些过于臃肿，在交互复杂的页面，打出来的 JS Bundle 的大小就更加不可控。

进入 `weex-bindingx` 源码发现，如果 native 直接使用 `requireModule('bindingx')` 引入，是需要改变 expression 为对象，把填写的表达式值传入对象的 origin 属性，然后传入一个的 `transformed` 属性，这个属性是很长的 CallNative 指令字符串，可以通过下载 npm 包 `bindindx-parser` 来自动生成。

于是 eros 做了很简单的二次封装，集成进 `widget` 中，直接通过 `this` 调用即可。

下面我们来实现一个官方的 demo：

![](/image/advanced_bindingx.gif)


首先编写模板和样式：

```
<template>
  <div class="container" >
    <div class="border">
      <div :ref="'my'" class="box" @touchstart="ontouchstart">
        <div class="head">
          <div class="avatar"></div>
          <text class="username">HACKER</text>
        </div>
        <div class="content">
          <text class="desc">Google announced a new version of Nearby Connections for fully offline.high bandwidth peer to peer device communications.</text>
        </div>
        <div class="footer">
          <text class="action">SHARE</text>
          <text class="action" style="color:#7C4DFF">EXPLORE</text>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
  .container {
    flex: 1;
    background-color:#eeeeee;
    
  }
  .border{
    height:1000px;
    padding-left:35px;
    padding-right:35px;
    padding-top:100px;
  }
  .box {
    width: 680px;
    height: 450px;
    background-color:#651FFF;
  }
  .head {
    background-color:#651FFF;
    width:680px;
    height:120px;
    flex-direction:row;
    align-items:center;
  }
  .content{
    width:680px;
    height:240px;
    background-color:#651FFF;
    padding-left:24px;
    padding-top:24px;
    padding-right:24px;
  }
  .footer {
    width:680px;
    height:90px;
    background-color: #fff;
    align-items:center;
    justify-content:flex-end;
    padding-right:25px;
    flex-direction:row
  }
  .action {
    font-size:35;
    padding-right:20px;
  }
  
  .desc {
    font-size:32;
    color:#fff;
    padding-left:24px;
  }
  
  
  .avatar {
    width:96px;
    height:96px;
    border-radius:48px;
    background-color:#CDDC39;
    margin-left:36px;
    margin-right:48px;
  }
  
  .username {
    color:#fff;
    font-size:32;
  }
  
</style>

```
编写 js 逻辑：
```
<script>
  export default {
    data () {
      return {
        x: 0,
        isInAnimation: false,
        opacity:1,
        gesToken:0
      }
    },
    methods: {
      ontouchstart (event) {
        // 如果在执行动画，就不触发
        if(this.isInAnimation) return 
        // 解绑动画
        if(this.gesToken != 0) {
          this.$bindingx.unbind({
            eventType:'pan',
            token:this.gesToken
          })
          this.gesToken = 0
          // return
        }
        // 找到元素 注意多了一个.ref
        let boxRef = this.$refs.box.ref
        let gesTokenObj = this.$bindingx.bind({
          anchor:boxRef,
          eventType:'pan',
          props: [
              {element:boxRef, property:'transform.translateX',expression:"x+0"}, // 这里是表达式，需要有运算符号，不能只写x
              {element:boxRef, property:'opacity',expression: "1-abs(x)/600"}]   // 我们这里期望 opacity 无论在拖动盒子向左向右的时候 都有渐隐 所有取绝对值 移动到绝对 600px 时候为全隐
        }, (e) => {
          if(e.state === 'end') {
            // 拖动结束事件 记录当前坐标 和 透明度
             this.x += e.deltaX
             this.opacity = 1-Math.abs(e.deltaX)/600
            //  开始进行下一步 回弹还是滑出的 动画
             this.bindTiming()
          }
        })
        // 记录下取消的token 多次 ontouchstart 时要带着 token 把上次的解绑
        this.gesToken = gesTokenObj.token
      },
      dismissCallback() {
        this.$notice.toast({
            message: '您已经删除了小卡片。'
        })
      },
      bindTiming() {
        // 开始执行动画
        this.isInAnimation = true

	      let boxRef = this.$refs.box.ref
        // 改变的 x 坐标，最终的 x 坐标，最终的透明值，位移 x 原点的表达式
        let changed_x, final_x, final_opacity, translate_x_origin
        // 通过一个变量来判断是否已经滑出
        let shouldDismiss = false

        // 生成表达式逻辑
        if(this.x>=-750/2 && this.x<=750/2) {
        // weex 设置宽度默认都是750px 往左拖动或者往右拖动盒子一半以内时
          shouldDismiss = false   // 标记为不消失
          final_x = 0             // 回到原点
          changed_x = 0-this.x    // 计算出需要位置的值
          final_opacity = 1       // 透明度变回 1
          translate_x_origin = `easeOutElastic(t,${this.x},${changed_x},1000)` // 运动曲线为easeOutElastic 生成位移到原点的表达式 1s内执行
        } else if(this.x < -750/2) {
          // 往左拖动盒子超过一半时
          shouldDismiss = true   // 标记为消失
          final_x = -750         // 完全把盒子位移到左边屏外面
          changed_x = -750-this.x// 计算出需要位置的值
          final_opacity = 0     // 透明度变回 0
          translate_x_origin = `easeOutExpo(t,${this.x},${changed_x},1000)` // 运动曲线为easeOutExpo 生成位移到 -750px 表达式 1s内执行
        } else {
          // 往右拖动盒子超过一半时
          shouldDismiss = true   // 标记为消失
          final_x = 750          // 完全把盒子位移到右边边屏外面
          changed_x = 750-this.x // 计算出需要位置的值
          final_opacity = 0      // 透明度变回 0
          translate_x_origin = `easeOutExpo(t,${this.x},${changed_x},1000)` // 运动曲线为easeOutExpo 生成位移到 750px 表达式 1s内执行
        }
       
      //  运动曲线为linear 计算出透明度表达式 1s内执行
       let opacity_origin = `linear(t,${this.opacity},${final_opacity - this.opacity},1000)`
	     let result = this.$bindingx.bind({
          eventType:'timing',       // 结束的时候是没有任何监听的 用 timing 来做定时的动画
          exitExpression:"t>1000",  // 当时间超过 10000ms 结束动画
          props: [
              {element:boxRef, property:'transform.translateX',expression:translate_x_origin},
              {element:boxRef, property:'opacity',expression:opacity_origin}
            ]
          
        },(e) => {
            if(e.state === 'end' || e.state === 'exit') {
              // reset x
              this.x = final_x
              this.opacity= final_opacity
              this.isInAnimation = false
              
              shouldDismiss && this.dismissCallback()
                
            }
        })
      }
    }
  }
</script>
```
可以看到，只需要调用 `$bindingx` 即可。

## weex native 中使用
**在非 eros 的 weex native 项目中**，我们可以重写 `bind` 方法来保持与官方使用一致，下载 npm 包， `lodash`和  `bindingx-parser` 来进行改造：

```
// bindingx.js
import { parse } from 'bindingx-parser'
import _cloneDeep from 'lodash/cloneDeep'

const WeexBinding = weex.requireModule('bindingx')
const BindingxFunction = WeexBinding.bind

let _WeexBinding = _cloneDeep(WeexBinding)

// 重写 bind 方法
_WeexBinding.bind = (options, callback) => {
    if (!options) {
        throw new Error('should pass options for binding')
    }

    options.exitExpression = formatExpression(options.exitExpression)

    if (options.props) {
        options.props.forEach((prop) => {
            prop.expression = formatExpression(prop.expression)
        })
    }

    return BindingxFunction(options, options && options.eventType === 'timing' ? fixCallback(callback) : callback)
}

module.export =  _WeexBinding
```
在业务中使用：
```
var bindingx = require('bindingx')
```
这样在打包之后非压缩状态下体积能减少到 `34kb` 左右。而 eros js bundle 的大小会更小，已经把这部分重写逻辑放入了 `widget`，通过 `appboard.js` 来内置到客户端执行。

现在，尽情使用 bindingx 吧！


