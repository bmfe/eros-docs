
# Introduction
**eros is based on [weex](https://weex-project.io/index.html) packaging front-facing vue wording solution, due to the particularity of app development, eros is more focused on the entire app project.**

| iOS DEMO built-in | ios version download | android version download |
|---------|---------|---------|
|![eros-demo](http://upload.ouliu.net/i/20180122162536pcw67.gif)|![ios Download](http://chuantu.biz/t6/214/1516790387x-1404793130.png)|![android download](http://upload.ouliu.net/i/201801241911376ee1z.png)|

* [iOS Download Link](http://fir.im/eros)
* [Android Download Link](https://fir.im/weexerosandroid)


> Developers can scan the QR code to download eros app to experience, iOS first need to open the App ** Settings -> General -> Device Management Trust Development Certificate.**

# You need to know before developing
**If you are a front-end developer:**
* Must be proficient in using vue development
* Be sure to read the weex documentation, especially with the web environment, vue development differences.
* In the development of client applications, there will be numerous environmental problems encountered, program problems, code problems, the vast majority of problems can be found on the Internet corresponding solutions, in the issue or issue, please try it yourself solve, eros environment installation can take a long time, you understand.
 -* Need to be familiar with the client development package process.
* Need to be familiar with the client development package process.

> Many front-end developers are halfway through environmental issues, but eros keeps improving their documentation, and there are plenty of developers in the development community who can help you answer it. Keep it up-to-date and rewarding.

**If you are a client developer:**

Then environment you will quickly set up, and then follow the familiar vue, weex documentation, because vue get started very quickly, and finally follow eros tutorial to write a few demo can start the development, you can easily expand the module.
# Essential knowledge
Before you develop you need to learn about weex knowledge, and be proficient with vue development, the documentation address is as follows.
* [weex](http://weex.apache.org/cn/guide/)
* [vue](https://cn.vuejs.org/v2/guide/)

# Support 
> Follow weex support, `but for the moment we do not support developing code compatible web-side`

* Android 4.1 (API 16)
* iOS 8.0+ 
* WebKit 534.30+ 

# eros 优点
eros advantages: 
* Detailed documentation to solve the trouble in construction environment。
* A set of code compiled into ios, android native application at both ends.
* Encapsulated a large number of weex module, so that front-end development to facilitate the native operation.
* Support a large number of echart examples running.
* Reduce code redundancy in multi-page projects with the appboard js bundle, reduce package size, and make real-time changes to it.
* Built a set of complete JS secondary package module (widget), injected directly through the vue plug-in system, directly called in this, according to the business to modify.
* Built-in intermediary service to interact with multiple pages and manage business.
* Built-in server-side incremental release update logic, can be used together with the open source incremental incremental release system.
* Support for real machines and simulators through `weex debug` breakpoint debugging, the program will be error log error prompts.

cli provides:
* Generate directly to the development of the latest template.
* Start the service for real-time development of view and debugging debugging.
* Update eros dependencies required for development platform.
* Direct `pack` platform built-in` JS Bundle`.
* Support for generating full package, incremental package, and built-in and update server interaction logic.
* Support synchronous update template content.
* Support weex vue entrance and js entrance of two development methods.

Support the popular component library:
* [weex-ui](https://github.com/alibaba/weex-ui) ( (Ali produced, recommended )
* [bui](https://github.com/bingo-oss/bui-weex)

# eros not enough
> eros development there are many restrictions, developers need to consider.

* -Although weex supports compiling three-terminal, ** eros does not currently support browser-side ** and currently only focuses on native.
* eros can not automatically use weex market temporarily, but if you have native development experience you can access.
* Because eros uses the appboard mechanism with the ** JS Bundle ** run-time mechanism to reduce the size of the js bundle, weex debug requires special processing.
*  If you encounter complex pages, such as IM, eros recommended native implementation, weex to cope with such needs is still relatively difficult.

# Principle view

![How it works](http://on-img.com/chart_image/59c5d743e4b0d34a18d69580.png)
[Big Picture Address](http://on-img.com/chart_image/59c5d743e4b0d34a18d69580.png)

# The status of open source
Currently, there are dozens of apps in development and on-line state, including the overseas application ** starLife (100+ pages) ** under development, as well as the domestic search ** (60+ pages ) **, there are ** applications are on the line 0-50 + page ranging **, involving the industry in information, medical, investment, shopping, government, office and so on.

Future releases will continue to showcase excellent projects to the home page, so developers can rest assured that this is not a KPI project (the company does not have KPIs) and has been open source based on the MIT protocol.


# Peripheral system
| Project | Description |
|---------|-------------|
| [eros-cli](https://github.com/bmfe/eros-cli) | Simple eros project building tools for building, developing. |
| [eros-publish](https://github.com/bmfe/eros-publish) | Simple server differential package update logic needs to be used with scaffolding. |
| [eros-ios-library](https://github.com/bmfe/eros-plugin-ios-baseLibrary) |  eros ios The Weex project depends on libraries. |
| [eros-ios-sdk](https://github.com/bmfe/WeexiOSSDK) | eros ios Weex sdk。 |
| [eros-android-framework](https://github.com/bmfe/WeexErosFramework) | eros weex Mobile Solutions Android Side Framework. |
| [eros-android-widget](https://github.com/bmfe/BMWidget) | eros Android Component Library. |
| [eros-android-sdk](https://github.com/bmfe/WeexSDK) | eros Android Mobile Solutions Android WexSDK. |

# 社区贡献
* [eros-yanxuan-demo](https://github.com/bmfe/eros-yanxuan-demo-v2)
* [weex-eros-book Reading app](https://github.com/wennjie/weex-book)
* [lygtq-eros-publish Server Incremental Publishing Logic](https://github.com/hodgevk/lygtq-eros-publish)
* [eros-node-server Server Incremental Publishing Logi](https://github.com/shawn-tangsc/eros-node-server)

# Discussion group
eros officially open source now has a development group of nearly 300 people (only by invitation), there are a large number of developers in the group have eros product development and has been on the line, in order to maintain a good environment, please Familiarity with the following group rules:

* This is not a traffic group, nor is it an advertising group, is to grow together to ensure the effectiveness of information, if the pornography gambling, discordant language, learning unrelated advertising, promotional content, irrelevant applets, not Will discuss, will be kicked directly, and no longer have the opportunity to enter.**
* Eros development encountered any problems can be sent to the group weex development can be discussed together.
* If you can not answer in a timely manner, please wait patiently, develop rich experience in the group of students will help you see the problem together.
* In addition to eros its own emergency BUG issues, we need to raise issues, we will deal with the issue, the purpose is to better reference for other developers, emergency issues thrown directly into the group, we will be directly to help you remotely debug.


**QQ group**:</br>
group1: **667379588** (full) <br>
group2: **798677297**

# License
[MIT](https://opensource.org/licenses/MIT)

Copyright (c) 2018-present, Byte Master Front End