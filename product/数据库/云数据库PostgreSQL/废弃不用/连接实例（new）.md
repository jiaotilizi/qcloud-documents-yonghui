当你的数据库实例初始化后，你可以使用任何标准的SQL客户端连接到该实例。接下来，我们提供一种使用pgAdmin连接PostgreSQL实例的示例。

## 第一步
（为了您的数据安全，目前仅提供了内网IP，因此您需要一个对应区域的云平台服务器）
你可以从 http://www.pgadmin.org/download/ 下载和安装pgAdmin。

## 第二步
点击“文件”菜单，选择“添加服务器”。

## 第三步
在“新建服务器登记”对话框中，填写名称、主机IP地址、端口号、用户名和密码。主机IP地址和端口号你可以从PGSQL实例管理页面中的“内网地址”找到。填写完成后点击“确定”。
![](http://imgcache.tcecqpoc.fsphere.cn/image/mccdn.qcloud.com/static/img/fd480ec9413eb6b7ff53d212fafd3ecd/image.png)
## 第四步
在左侧的对象浏览器菜单中，双击“服务器组”展开该列表。选择刚刚连接好的服务器（数据库实例），并选择数据库的名称。
![](http://imgcache.tcecqpoc.fsphere.cn/image/mccdn.qcloud.com/static/img/a66c259deee8524a0cf35cb3c5e29642/image.jpg)
## 第五步
启动PSQL控制台，首先点击如图所示的图标，选择PSQL CONSOLE。即可在控制台中操作你的数据库了。
![](http://imgcache.tcecqpoc.fsphere.cn/image/mccdn.qcloud.com/static/img/21e18377780799f20c40c48421c041a1/image.jpg)

