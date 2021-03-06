# MobileLine iOS 移动存储服务快速入门

移动开发平台（MobileLine）使用起来非常容易，只需要简单的 4 步，您便可快速接入移动崩溃监测。接入后，您即可获得我们提供的各项能力，减少您在开发应用时的重复工作，提升开发效率。

## 准备工作

为了使用移动开发平台（MobileLine）iOS 版本的 SDK，您首先需要一个 iOS 工程，这个工程可以是您现有的工程，也可以是您新建的一个空的工程。

## 第一步：创建项目和应用


在使用我们的服务前，您必须先在 MobileLine 控制台上 [创建项目和应用](/document/product/666/15345)。

> 如果您已经在 MobileLine 控制台上创建过了项目和应用，请跳过此步。


## 第二步：添加配置文件

> 如果您已经添加过配置文件，请跳过此步。

创建好应用后，您可以点击红框中的【tac\_services\_configurations.zip】来下载该应用的配置文件的压缩包：

![](http://ws2.sinaimg.cn/large/006tNc79gy1fq0pubol92j31kw093gnw.jpg)

解压后将 tac_services_configurations.plist 文件集成进项目中。其中有一个  tac_services_configurations_unpackage.plist 文件，请将该文件放到您工程的根目录下面(**切记不要将改文件添加进工程中**)。 添加好配置文件后，继续点击【下一步】。


![](http://imgcache.tcecqpoc.fsphere.cn/image/ws1.sinaimg.cn/large/006tNc79gy1forbnw3ijyj31bi11wnch.jpg)

> 切记**不要**将文件 `tac_service_configurations_unpackage.plist` 添加进工程，文件中包含了不可泄露的机密信息，请不要打包到 apk 文件中，MobileLine SDK 也会对此进行检查，防止由于您误打包造成的机密信息泄露。




## 第三步：集成 SDK


如果还没有 Podfile，请创建一个。

~~~
$ cd your-project directory
$ pod init
~~~

并在您的 Podfile 文件中添加移动开发平台（MobileLine）的私有源：

~~~
source "http://git.tce.fsphere.cn/qcloud_u/cocopoads-repo"
source "http://github.com/CocoaPods/Specs"
~~~

在 Podfile 中添加依赖：

```
pod 'TACStorage'
```

### 配置程序需要脚本

> 如果您在其他模块中完成了此步骤，请不要重复执行。

为了极致的简化 SDK 的接入流程我们，使用 shell 脚本，帮助您自动化的去执行一些繁琐的操作，比如 crash 自动上报，在 Info.plist 里面注册各种第三方 SDK 的回调 scheme。因而，需要您添加以下脚本来使用我们自动化的加入流程。

脚本主要包括两个：

1. 在构建之前运行的脚本，该类型的脚本会修改一些程序的配置信息，比如在 Info.plist 里面增加 qqwallet 的 scheme 回调。
2. 在构建之后运行的脚本，该类型的脚本在执行结束后做一些动作，比如 Crash 符号表上报。

![](http://imgcache.tcecqpoc.fsphere.cn/image/ws1.sinaimg.cn/large/006tNc79ly1fnttw83xayj317i0ro44j.jpg)

请按照以下步骤来添加脚本：

##### 添加构建之前运行的脚本

1. 在导航栏中打开您的工程。
2. 打开 Tab `Build Phases`。
3. 点击 `Add a new build phase` , 并选择 `New Run Script Phase`，您可以将改脚本命名 TAC Run Before
> **注意：**
请确保该脚本在 `Build Phases` 中排序为第二。
4. 根据自己集成的模块和集成方式将代码粘贴入  `Type a script...` 文本框:。

需要黏贴的代码

~~~
#export TAC_SCRIPTS_BASE_PATH=[自定义执行脚本查找路径，我们会在该路径下寻找所有以“tac.run.all.before.sh”命名的脚本，并执行，如果您不需要自定义不用动这里]
${TAC_CORE_FRAMEWORK_PATH}/Scripts/tac.run.all.before.sh
~~~

其中 `THIRD_FRAMEWORK_PATH` 变量的取值根据您的安装方式而不同：

* 如果您使用 Cocoapods 来集成的则为 `${PODS_ROOT}/TACCore`，您需要黏贴的代码实例如下：
   
  ~~~
  ${SRCROOT}/Pods/TACCore/Scripts/tac.run.all.before.sh
  ~~~
* 如果您使用手工集成的方式则为 `您存储 TACCore 库的地址`，即您 TACCore framework 的引入路径，您需要黏贴的代码实例如下：
   
  ~~~
   export TAC_SCRIPTS_BASE_PATH=[自定义执行脚本查找路径，我们会在该路径下寻找所有以“tac.run.all.after.sh”命名的脚本，并执行，如果您不需要自定义不用动这里]
   [您存储 TACCore 库的地址]/TACCore.framework/Scripts/tac.run.all.before.sh
  ~~~


##### 添加构建之后运行的脚本

1. 在导航栏中打开您的工程。
2. 打开 Tab `Build Phases`。
3. 点击 `Add a new build phase` , 并选择 `New Run Script Phase`，您可以将改脚本命名 TAC Run Before。
> **注意：**
>  请确保该脚本在 `Build Phases` 中排序需要放到最后。
4. 根据自己集成的模块和集成方式将代码粘贴入  `Type a script...` 文本框:。

需要黏贴的代码

~~~
#export TAC_SCRIPTS_BASE_PATH=[自定义执行脚本查找路径，我们会在该路径下寻找所有以“tac.run.all.after.sh”命名的脚本，并执行，如果您不需要自定义不用动这里]
${TAC_CORE_FRAMEWORK_PATH}/Scripts/tac.run.all.after.sh
~~~

其中 `THIRD_FRAMEWORK_PATH` 变量的取值根据您的安装方式而不同：

* 如果您使用 Cocoapods 来集成的则为 `${PODS_ROOT}/TACCore`，您需要黏贴的代码实例如下：
	
  ~~~
  ${SRCROOT}/Pods/TACCore/Scripts/tac.run.all.after.sh
  ~~~
* 如果您使用手工集成的方式则为 `[您存储 TACCore 库的地址]`，即您 TACCore framework 的引入路径，您需要黏贴的代码实例如下：
    
  ~~~
  #export TAC_SCRIPTS_BASE_PATH=[自定义执行脚本查找路径，我们会在该路径下寻找所有以“tac.run.all.after.sh”命名的脚本，并执行，如果您不需要自定义不用动这里]
  [您存储 TACCore 库的地址]/TACCore.framework/Scripts/tac.run.all.after.sh
  ~~~


## 第四步：初始化

集成好我们提供的 SDK 后，您需要在您自己的工程中添加初始化代码，从而让 MobileLine 服务在您的应用中进行自动配置。整个初始化的过程很简单。

### 步骤 1 在 UIApplicationDelegate 子类中导入移动开发平台（MobileLine）模块。

Objective-C 代码示例：

~~~
#import <TACCore/TACCore.h>
~~~
Swift 代码示例：

~~~
import TACCore
~~~


### 步骤 2 配置一个 TACApplication 共享实例，通常是在应用的 `application:didFinishLaunchingWithOptions:` 方法中配置。


######  使用默认配置

通常对于移动开发平台（MobileLine）的项目他的配置信息都是通过读取 tac_services_configuration.plist 文件来获取的。

Objective-C 代码示例：

~~~
    [TACApplication configurate];
~~~

Swift 代码示例：

~~~
	TACApplication.configurate();
~~~


### 配置 TACStorage 的使用权限。


请先参考[快速搭建移动应用传输服务](/document/product/436/9068) 搭建用于构建临时密钥的服务器。

TACStorage 后台为云平台 COS 服务，在使用 COS 服务的时候需要对请求进行权限校验，来确保对应的请求是否有权限访问对应的资源。因而您需要在您的代码中实现 `QCloudCredentailFenceQueueDelegate` 协议来提供相关的权限信息。

~~~
@interface TACStorageDemoViewController () <QCloudCredentailFenceQueueDelegate>
@end

@implementation  TACStorageDemoViewController
- (void) fenceQueue:(QCloudCredentailFenceQueue *)queue requestCreatorWithContinue:(QCloudCredentailFenceQueueContinue)continueBlock
{
    QCloudCredential* crendential = [[QCloudCredential alloc] init];

    // 在调试阶段您可以通过直接设置secretID和secretKey来测试服务，但是强烈不建议在线上环境使用该方式！！！
#ifdef ! DEBUG
    QCloudCredential* crendential = [[QCloudCredential alloc] init];
    crendential.secretID = <#secretID#>;
    crendential.secretKey = <#secretKey#>;
    QCloudAuthentationV5Creator* creator = [[QCloudAuthentationV5Creator alloc] initWithCredential:crendential];
    continueBlock(creator, nil);
#else

    //您需要配置自己的服务器，来获取CAM临时密钥。并通过临时密钥来创建权限Creator。具体可以参考：[快速搭建移动应用传输服务](/document/product/436/9068)
    void(^NetworkCall)(id response, NSError* error) = ^(id response, NSError* error) {
        if (error) {
            continueBlock(nil, error);
        } else {
            QCloudCredential* crendential = [[QCloudCredential alloc] init];
            crendential.secretID = @"AKIDPiqmW3qcgXVSKN8jngPzRhvxzYyDL5qP";
            crendential.secretKey = @"EH8oHoLgpmJmBQUM1Uoywjmv7EFzd5OJ";
            crendential.experationDate = nil;
            crendential.token = ;
            QCloudAuthentationV5Creator* creator = [[QCloudAuthentationV5Creator alloc] initWithCredential:crendential];
            continueBlock(creator, nil);
        }

    };
    <#do network with callback:NetworkCall #>
#endif
}
@end
~~~




## 启动服务

移动存储服务无需启动，到此您已经成功接入了 MobileLine 移动存储服务。




## 后续步骤


### 了解 MobileLine：

- 查看 [MoblieLine 应用示例](http://github.com/tencentyun/qcloud-sdk-ios-samples/tree/master/MobileLineDemo)

### 向您的应用添加 MobileLine 功能：

- 借助 [Analytics](/document/product/666/14822) 深入分析用户行为。
- 借助 [messaging](/document/product/666/14826) 向用户发送通知。
- 借助 [crash](/document/product/666/14824) 确定应用崩溃的时间和原因。
- 借助 [storage](/document/product/666/14828) 存储和访问用户生成的内容（如照片或视频）。
- 借助 [authorization](/document/product/666/14830) 来进行用户身份验证。
- 借助 [payment](/document/product/666/14832) 获取微信和手 Q 支付能力
