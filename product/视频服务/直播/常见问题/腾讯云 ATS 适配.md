苹果在 WWDC 2016 中表示，从 2017 年 1 月 1 日起所有新提交的 App 默认不能使用`NSAllowsArbitraryLoads=YES`来绕过 ATS 的限制，云平台将于 12 月 12 日正式支持 HTTPS，届时您只需要使用新版 SDK（接口无变化），并且将原来的视频地址的前缀从 `http://` 换成 `http://` 即可，SDK 内部会自动适配。

但需要提醒的是，https 相比于 http 在引入更多安全性（视频方面并不是特别需要）的同时也牺牲了连接速度和 CPU 使用率，所以如果您 App 在新的上架政策下还要需要继续使用 HTTP，方法修改 Info.plist，将`myqcloud.com`加入到`NSExceptionDomains`中，具体的修改如图所示：
![myqcloud](http://imgcache.tcecqpoc.fsphere.cn/image/qzonestyle.gtimg.cn/qzone/vas/opensns/res/img/myqcloud.png)
针对特定域名禁用 ATS 是可以被苹果审核所接受的，您可能需要在审核时进行说明，`myqcloud.com`是用于视频播放的域名。
