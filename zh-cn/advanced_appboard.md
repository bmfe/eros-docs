## 为什么会有 appboard ？
我们知道 weex 推荐通过多页面的形式来构建我们的应用，那么常见的传统多页面造成的问题自然也需要我们去解决，其中影响最大是就是：

> 所有页面的公共依赖直接会影响的 bundle 体积

100 个页面都依赖某些公共模块 `commin.js`，假设 `commin.js` 大小是 `10 kb`，如果你拆分的不够细，或者写法不支持 tree shaking，那么你每个页面中都会含有这 `10 kb` 的代码，总大小 `1000 kb`，分析一下：

> 1000kb = 10kb + 990kb（冗余）

我只想要打包体积只增长 `10kb`，不要剩余的 `990kb 冗余`，`appboard` 就是帮你减少冗余的。

## 什么是 appboard ？
我们把 board 翻译成为`寄宿`就会好理解，意思就是我们想寄宿在 app 上，他的本质就是一个 `js bundle`，原理大家也不难猜出：

> 把这些公共逻辑打成一个 `js bundle`，在客户端执行业务 bundle 之前，执行公共逻辑的 `js bundle` 即可。

## 使用
> 我们在开发模板中默认帮你配置了 `公共 js bundle` 为 `src/js/config/index.js`，**一般来说你无需进行以下手动配置，直接使用即可**。

1.在 `eros.dev.js` 中配置出你想要成为公共 js bundle 的路径：

```js
'exports': [
		// appBoard
        'js/config/index.js',
        ...
]
```

2.在 `eros.native.js` 注册，写入 src/js/ 的相对路径。

```js
{
	...
	'appBoard': '/config/index.js',
	...
}
```

3.用上面的例子来说，我只想寄宿这`10kb`，我只需把代码放入 `src/js/config/index.js` 中即可。

## 建议
> 聪明的你应该已经发现，我们的 widget 就是这样的方式引入进来的，widget 总共大小有 50 多kb，但每个业务 bundle 中并未冗余。

但我们还有几点需要注意的：
- **不要往里面放过多的代码，必定会增加每个 bundle 的解析执行的时间！**
- 我们建议公共代码抽离之后放入 `appboard`，非公共的还是在业务 bundle 中自行引入。
- 而你无需在业务的 `js bundle` 中做类似 `import` 这个 `config/index.js` 的代码，因为我们已经在客户端内帮你拼接了。
- 这里路径相差了个 `js` 字符，先记一下，后续会在优化，保持一致。
