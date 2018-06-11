# App名称、版本号设置

1. 打开 iOS 工程，选择工程目录
2. 点击WeexEros 工程文件
3. 在 TARGERS 中选择 WeeEros
4. 选择 Genneral 设置项

如下图![](https://img.benmu-health.com/gitbook/appname.png)

#### 说明

Display Name：App 安装到手机上显示的名称；

Bundle Identifier：App 唯一标示（开发者后台配置的AppID） 

Version：版本号

Build：Build 号


### 如何修改iOS工程名

我们刚刚使用eros init出来的项目，工程默认是这样的
![](https://ws4.sinaimg.cn/large/006tKfTcgy1fs607kf4sgj30f20esmxe.jpg)

如果你是“强迫症患者”，你一定会说想把工程名字改成你想要的名字。就像这样：
![](https://ws3.sinaimg.cn/large/006tKfTcgy1fs609atdlvj308e0e9weo.jpg)

那这个时候，就需要执行下面步骤就可以了

* 首先，用xcode打开项目，在工程目录中，长按项目名，修改项目名

* 然后，xcode会跳出一个一个谈话框，这里他会告诉你有一些文件需要跟着一起修改，你只要选择rename就可以了。

* 之后，你可以修改项目下的weexEros 文件夹，修改成和你项目名一样,同时也可以修改.elements文件名。

* 这个时候要注意了！！！请到build setting 里，搜索WeexEros ，然后将对应的地方全部修改成你改动过后的名字。

* 现在，你可以关掉xcode，然后进入ios的工程目录，修改podfile的target

```
...
target '<yourProject>' do
    common
end
```
* 再去修改install.sh 中对应的命令：

```
...
open <yourProjectName>.xcworkspace
```

* 最后，回到你的eros项目内，执行。

```
eros install --ios
```

* 至此，你已经成功的修改了eros 默认的项目名称，并且打开了你自己的ios project，但是，启动的时候会报错，这个时候：**你就需要进入的ios项目中》打开你的general  》找到Linked Frameworks and Libraries 》 删除libPods-WeesErosApp.a （你也可以对应的删掉Frameworks 中的引入文件）**

* ***注意！！！*** 这里需要你再最后修改一个地方，那就是eros.native.js中的zipFolder.iOS否则npm run pack 的时候会报错。

```
 'zipFolder': {
        'iOS': '/ios/WeexEros/<yourProjectName>',
        ...
    },
```


* 启动，这个时候就可以正常启动成功了。
