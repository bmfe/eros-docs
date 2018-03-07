## Q: phone cannot online after setting agent

A:

1.First check the documentation for`development and debugging`,Check to see if the agent tool is open（charles、fiddler or others）,If not open, please open the relevant agent tools；

- Charles Use tutorials：http://www.jianshu.com/p/fdd7c681929c
- Fiddler Use tutorials：http://www.jianshu.com/p/99b6b4cd273c

## Q: eros dev development service unsuccessful

A:
if report an error
```
...
webpackRun.run is not a function
...
```
please `cd` in your new project to try

**mac/linux:**
Normally mac/linux there will not be any problem，if prompted to insufficient permissions, please use `sudo`。

**windows**
if prompted to insufficient permissions，if `dev.json` not modified.The default is port 80，Please find out 80 port occupation and shut down him。

If reported the following error:
> vue packages version mismatch

Please install weex-toolkit globally ，and run：
```
weex repair
```

## Q: Why is my current js bundle so big

A:
Currently, scaffolding is integrating tree-shaking，because the official did not use tree-shaking to optimize package size，so it takes time。

Currently, you can optimize first：
```
// Currently：
import {buiButton} from 'Eros/bui'

// instead of：
import buiButton from 'Eros/bui/components/buiButton'
```

This can significantly reduce the package size.

## Q: iOS resources download slow，or waiting time is huge（command: eros install ios）

A:

After performing ` eros install ios ` command will load from the git ios project required tripartite SDK，Because of network reason, or is the reason of wall some resources load huge slow. Even failed to load as shown below：

![](./image/iosInstallError.jpeg)

**Solution**:Download the resource file from https://github.com/xiaohuapunk/WeexEros3Rd to the local，And copy (your project directory)/platforms/ios/WeexEros, and then run the eros install ios command again；

## Q: IOS template engineering certificateless real machine debugging

A:

> Since the release of Xcode7, Apple has allowed developers to debug without a license (there are some differences with the certificate, many functions are not used, such as push), a few steps can be achieved without certificate real machine debugging；

##### 1.Registered personal AppleID account

- Log in to the developer background (https://developer.apple.com/) register an AppleID, if you have an account (that is, download the app log in to the account) to log in, agree to the terms;

- In the Xcode account settings, add an account

![](./image/addAccounts.png)

##### 2No certificate real machine debugging need to close the `Push Notifications` function; otherwise it will report the following error message👇

> Our template opened the push function, so you need to turn this feature off, personal account is not the function
<br/>

![](http://ww4.sinaimg.cn/large/0060lm7Tly1fmlbm99iibj30i40amjt1.jpg)

Solution：

- Modify `Bundle Identifier` note：Need to be modified into something not used by others, Apple will verify the uniqueness

![](http://ww2.sinaimg.cn/large/0060lm7Tly1fmlbugnl9uj313w04m74s.jpg)

- modify`team`，choose `None`（Need to set `team` to `none`） 

![](http://ww3.sinaimg.cn/large/0060lm7Tly1fmlby2fkcnj313y0cuabw.jpg)

- Find Gapabilities，Turn `Push Notifications` off

![](http://ww2.sinaimg.cn/large/0060lm7Tly1fmlc0xwiorj31840cqtb6.jpg)

##### 3.Set Team to your own account

##### Upgrade engineering files
> remember to back up the project before updating <br>
> Some updates need to update the project file, not updated by `instal`, you need to use` eros update` command；<br>

After the terminal type `eros update` enter the file path prompts to update

The following is each file path
- install.sh: `/platforms/ios/WeexEros/install.sh`
- Info：`/platforms/ios/WeexEros/WeexEros/Info.plist`
- Podfile：`/platforms/ios/WeexEros/Podfile`
- AppDelegate.m: `/platforms/ios/WeexEros/WeexEros/AppDelegate.m` <br>
After the upgrade needs to be execute again `eros install ios`

##### Remove ATSDK
`Weex` default `integration of the ATSDK` performance monitoring tools, `Eros` also integrated the sdk before, sdk private api used, will result in the audit does not pass, `Eros` has now removed it, if you have been using the `Eros` iOS app developed below Is the removal method:

> 2017.12.22 Initialization of the project does not require this operation

##### 1.Upgrade Podfile file

- Podfile：`eros update`，The path to fill in `/platforms/ios/WeexEros/Podfile`.


##### 2.After upgrading, you need to run `eros install ios` again
##### 3.Modify the configuration file

> In Xcode: Find the `Other Linker Flags` and expand it, remove the `ATSDK` references in Debug and Release, and note that you need to also remove the `-framework` above

![](./image/removeAtsdk.png)

##### 4.Re-run, successful operation shows that the removal was successful

## Q: errCode->wx_network_error msg->Unable to resolve host 'app.weex-eros.com':No address associated with hostname 

A:
If this error proves that your phone or emulator is unable to connect to your service, you need to check the following to troubleshoot why you can not connect to the service.
1. Confirm service is open, you can access services such as: app.weex-eros.com: 8889 (if the port is not the same or need to change the corresponding）.
2. If the computer can normally visit, then use the mobile browser to continue to view.
3. If the phone browser is not accessible then you may need to connect to the proxy.
4. If you set the proxy still can not access, please confirm whether the proxy is set up successfully, you can try to use mobile phone to access Baidu, whether it can visit, and the agent captured successfully.
5. If you do not want to set up an agent, try accessing your service directly from your phone's browser using ip (if ported plus), if you can directly access it successfully.You can change the jsServer field in your eros-demo/config /eros.native.js file to your ip (plus port if you remember).
Then please re-pack running Android program


## Q: Android how to modify the package name (the same phone can run 2 eros project).

A: Modify gradle.properties file APPLICATION_ID (gradle.properties file in the project with the directory).


## Q: Failed to resolve: com.android.support:appcompat
```
Failed to resolve: com.android.support:appcompat-v7:25.3.1 
Add Google Maven repository and sync project 
Show in File
Show in Project Structure dialog 
```
A： At this time you can click Add Google Maven repository and sync project to wait for the compilation to finish
