## 开发准备

### 相关资源

[github项目](http://github.com/tencentyun/cos-cpp-sdk-v4)

### 开发环境

1. 安装openssl的库和头文件 [http://www.openssl.org/source/](http://www.openssl.org/source/) 
2. 安装curl的库和头文件 [http://curl.haxx.se/download/curl-7.43.0.tar.gz](http://curl.haxx.se/download/curl-7.43.0.tar.gz) 
3. 安装jsoncpp的库和头文件 [http://github.com/open-source-parsers/jsoncpp](http://github.com/open-source-parsers/jsoncpp) 
4. 安装boost的库和头文件 [http://www.boost.org/](http://www.boost.org/) 
5. 安装cmake工具 [http://www.cmake.org/download/](http://www.cmake.org/download/) 
6. 从控制台获取APP ID、SecretID、SecretKey，详情参考[权限控制](doc/api/264/5993)。



注意：

1. sdk中提供了curl和jsoncpp的库以及头文件，以上库编译好后替换掉sdk中相应的库和头文件即可，如果以上库已经安装到系统里，也可删除sdk中相应的库和头文件。
2. curl默认不支持多线程环境，如果项目使用多线程，在编译curl执行 configure 时需指定 --enable-ares 参数来开启异步DNS解析，依赖 c-ares库，如果系统没有，可到[http://c-ares.haxx.se/](http://c-ares.haxx.se/) 下载安装。
3. jsoncpp的1.y.x版本需要c++11的支持，如果编译器不支持，可以换成 0.y.x版本。



### SDK 配置

直接下载github上提供的源代码，集成到您的开发环境。 

执行下面的命令 ：

``` 
cd ${cos-cpp-sdk} 
mkdir -p build 
cd build 
cmake .. 
make 
```

cos_demo.cpp里面有常见API的例子。生成的cos_demo可以直接运行，生成的静态库名称为：libcossdk.a。生成的 libcossdk.a 放到你自己的工程里lib路径下，include 目录拷贝到自己的工程的include路径下。 



## 生成签名

### 多次有效签名

#### 方法原型

``` c++
static string AppSignMuti(const uint64_t appId, 
                          const string &secretId, 
                          const string &secretKey, 
                          const uint64_t expired_time, 
                          const string &bucketName); 
```

#### 参数说明

| 参数名          | **类型**   | **是否必填** | **默认值** | **参数描述**     |
| ------------ | -------- | -------- | ------- | ------------ |
| appId        | uint64_t | 是        | 无       | 项目APP ID     |
| secretId     | String   | 是        | 无       | 用户 Secret ID |
| secretKey    | String   | 是        | 无       | 用户 SecretKey |
| expired_time | uint64_t | 否        | 无       | 过期时间，Unix时间戳 |
| bucketName   | String   | 否        | 无       | bucket名称     |

#### 返回结果说明

返回值：签名字符串

#### 示例

``` c++
uint64_t expired = time(NULL) + 60;
string sign = Auth::AppSignMuti(10000000,"SecretId","SecretKey",expired,"bucketName");
```

### 单次有效签名

#### 方法原型

``` c++
static string AppSignOnce(const uint64_t appId, 
                          const string &secretId, 
                          const string &secretKey, 
                          const string &path, 
                          const string &bucketName); 
```

#### 参数说明

| **参数名**    | **类型**   | **必须** | **默认值** | **参数描述**                                 |
| ---------- | -------- | ------ | ------- | ---------------------------------------- |
| appId      | uint64_t | 是      | 无       | 项目 APP ID                                |
| secretId   | String   | 是      | 无       | 项目 SecretID                              |
| secretKey  | String   | 是      | 无       | 项目 SecretKey                             |
| bucketName | String   | 否      | 无       | bucket名称                                 |
| path       | String   | 是      | 无       | 文件路径，以斜杠开头，例如/filepath/filename，为文件在此bucketname下的全路径 |

#### 返回值说明

返回值：签名字符串

#### 示例

``` c++
string path= "/myFloder/myFile.rar";
sign = Auth::AppSignOnce(10000000, "SecretId", "SecretKey", path, bucketName);
```

更多签名相关详细说明，请参考[权限控制](doc/api/264/5993)。

## 初始化操作

### 初始化

接口说明：在使用COS操作之前，需要首先进行COS系统参数的设置，然后分别创建CosConfig以及CosAPI对象，COS的操作都是基于CosAPI对象进行的。

### 配置文件

``` c++
"AppID":********,
"SecretID":"*********************************",
"SecretKey":"********************************",
"Region":"sh",   //COS区域, 上传和下载域名均是跟此有关系，因此一定要保证正确
"SignExpiredTime":360, //签名超时时间
"CurlConnectTimeoutInms":180,  //http超时时间
"CurlGlobalConnectTimeoutInms":360, //
"UploadSliceSize":1048576, //文件分片大小，可选值有524288、1048576、2097152、3145728
"IsUploadTakeSha":0, //文件上传时是否携带文件sha值，0：不携带，1：携带
"DownloadDomainType":2, //下载域名类型,1:cdn,2:cos,3:innercos,4:自定义域名
"SelfDomain":"",//自定义域名
"UploadThreadPoolSize":5, //单文件分片上传线程池大小
"AsynThreadPoolSize":2, //异步上传下载线程池大小
"LogoutType":1 //日志输出类型,0:不输出,1:输出到屏幕,2输出到syslog
```

### COS API对象构造原型

``` c++
CosConfig(const string& config_file);
CosAPI(CosConfig& config);
```



## 目录操作

### 创建目录

接口说明：用于目录的创建，调用者可以通过此接口在指定bucket下创建目录。

#### 方法原型

``` c++
string CosAPI::FolderCreate(FolderCreateReq& request);        
```

#### 参数说明

| **参数名** | **类型**          | **默认值** | **参数描述** |
| ------- | --------------- | ------- | -------- |
| request | FolderCreateReq | 无       | 目录创建请求类型 |

| request成员 | 类型     | 默认值  | 设置方法 | 描述       |
| --------- | ------ | ---- | ---- | -------- |
| bucket    | string | 无    | 构造函数 | bucket名称 |
| cosPath   | string | 无    | 构造函数 | 上传的目标路径  |
| bizAttr   | string | 空字符串 | 构造函数 | 文件夹属性    |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **参数描述**                   |
| ------- | ------ | -------------------------- |
| code    | Int    | 返回码，成功时为0                  |
| message | String | 描述信息                       |
| data    | Array  | 返回数据，请参考《Restful API 创建目录》 |

#### 示例

``` c++
FolderCreateReq folderCreateReq(bucket,folder,folder_biz_attr);
string rsp = cos.FolderCreate(folderCreateReq);
```

### 目录更新

接口说明：用于目录业务自定义属性的更新，可以通过此接口更新业务的自定义属性字段。

#### 方法原型

``` c++
string FolderUpdate(FolderUpdateReq& request);                    
```

#### 参数说明

| **参数名** | **类型**          | **默认值** | **参数描述** |
| ------- | --------------- | ------- | -------- |
| request | FolderUpdateReq | 无       | 目录更新请求类型 |

| **参数名** | **类型** | **默认值** | **设置方法** | **参数描述** |
| ------- | ------ | ------- | -------- | -------- |
| bucket  | String | 无       | 构造函数     | bucket名称 |
| cosPath | String | 无       | 构造函数     | 目录的全路径   |
| bizAttr | string | 空       | 构造函数     | 文件夹属性    |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **参数描述**                   |
| ------- | ------ | -------------------------- |
| code    | Int    | 返回码，成功时为0                  |
| message | String | 描述信息                       |
| data    | Array  | 返回数据，请参考《Restful API 目录更新》 |

#### 示例

``` 
FolderUpdateReq folderUpdateReq(bucket,folder, folder_biz_attr);
string rsp = cos.FolderUpdate(folderUpdateReq);
```

### 目录查询

接口说明：用于目录属性的查询，可以通过此接口查询目录的属性。

#### 方法原型

``` c++
string CosAPI::FolderStat(FolderStatReq& request);
```

#### 参数说明

| **参数名** | **类型**        | **默认值** | **参数描述** |
| ------- | ------------- | ------- | -------- |
| request | FolderStatReq | 无       | 目录查询请求类型 |

| **参数名** | **类型** | **默认值** | **设置方法** | **参数描述** |
| ------- | ------ | ------- | -------- | -------- |
| bucket  | String | 无       | 构造函数     | bucket名称 |
| cosPath | String | 无       | 构造函数     | 目录的全路径   |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **参数描述**  |
| ------- | ------ | --------- |
| code    | Int    | 返回码，成功时为0 |
| message | String | 描述信息      |

#### 示例

``` c++
FolderStatReq folderStatReq(bucket,folder);
string rsp = cos.FolderStat(folderStatReq);
```

### 目录删除

接口说明：用于目录的删除，可以通过此接口删除空目录，如果目录中存在有效文件或目录，将不能删除。

#### 方法原型

``` c++
string CosAPI::FolderDelete(FolderDeleteReq& request);
```

#### 参数说明

| **参数名** | **类型**          | **默认值** | **参数描述** |
| ------- | --------------- | ------- | -------- |
| request | FolderDeleteReq | 无       | 目录删除请求类型 |

| **参数名** | **类型** | **默认值** | **设置方法** | **参数描述** |
| ------- | ------ | ------- | -------- | -------- |
| bucket  | String | 无       | 构造函数     | bucket名称 |
| cosPath | String | 无       | 构造函数     | 目录的全路径   |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **参数描述**  |
| ------- | ------ | --------- |
| code    | Int    | 返回码，成功时为0 |
| message | String | 描述信息      |

#### 示例

``` c++
FolderDeleteReq folderDeleteReq(bucket,folder);
string rsp = cos.FolderDelete(folderDeleteReq);
```

### 目录列表

接口说明：用于列举目录下文件和目录，可以通过此接口查询目录下的文件和目录属性。

#### 方法原型

``` c++
string CosAPI::FolderList(FolderListReq& request);
```

#### 参数说明

| **参数名** | **类型**        | **默认值** | **参数描述** |
| ------- | ------------- | ------- | -------- |
| request | FolderListReq | 无       | 目录列表请求类型 |

| **参数名** | **类型** | **默认值** | **设置方法** | **参数描述**                                 |
| ------- | ------ | ------- | -------- | ---------------------------------------- |
| bucket  | String | 无       | 构造函数     | bucket名称                                 |
| cosPath | String | 无       | 构造函数     | 目录的全路径                                   |
| listNum | int    | 1000    | 构造函数     | 查询数目，最大为1000                             |
| context | string | 空字符串    | 构造函数     | 查询上下文。查看第一页，则传空字符串；若需翻页，需要将前一页查询响应中的context设置到参数中 |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **是否必然返回** | **参数描述**                   |
| ------- | ------ | ---------- | -------------------------- |
| code    | Int    | 是          | 返回码，成功时为0                  |
| message | String | 是          | 描述信息                       |
| data    | Array  | 是          | 返回数据，请参考《Restful API 目录列表》 |

#### 示例

``` c++
FolderListReq folderListReq(bucket,folder);
string rsp = cos.FolderList(folderListReq);
```

## 文件操作

### 文件上传

接口说明：文件上传，包括单文件上传和分片上传（大于8M的文件需要通过将文件内容进行分片上传）。

#### 方法原型

``` c++
string CosAPI::FileUpload(FileUploadReq& request);
```

#### 参数说明

| **参数名** | **类型**          | **默认值** | **参数描述** |
| ------- | --------------- | ------- | -------- |
| request | FolderUploadReq | 无       | 文件上传请求类型 |

| **参数名**    | **类型** | **默认值** | **设置方法**             | **参数描述**                               |
| ---------- | ------ | ------- | -------------------- | -------------------------------------- |
| bucket     | String | 无       | 构造函数                 | bucket名字                               |
| srcPath    | String | 无       | 构造函数                 | 待上传的本地文件路径                             |
| cosPath    | String | 无       | 构造函数                 | 文件的全路径                                 |
| bizAttr    | string | 无       | setBizAttr()         | 文件属性                                   |
| insertOnly | int    | 1       | 构造函数及setInsertOnly() | 同名文件是否覆盖。0：表示覆盖同名文件，1：表示不覆盖, 当文件存在返回错误 |
| sliceSize  | int    | 1048576 | setSliceSize()       | 分片大小，可选值512K/1M/2M/3M；默认1M，均需转换为字节数值   |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **是否必然返回** | **参数描述**                   |
| ------- | ------ | ---------- | -------------------------- |
| code    | Int    | 是          | 返回码，成功时为0                  |
| message | String | 是          | 描述信息                       |
| data    | Array  | 否          | 返回数据，请参考《Restful API 文件上传》 |

#### 示例

``` c++
FileUploadReq fileUploadReq(bucket, srcPath, dstPath);
string rsp = cos.FileUpload(fileUploadReq);
```

### 文件更新

接口说明：用于目录业务自定义属性的更新，可以通过此接口更新业务的自定义属性字段。

#### 方法原型

``` C++
string CosAPI::FileUpdate(FileUpdateReq& request);
```

#### 参数说明

| **参数名** | **类型**        | **默认值** | **参数描述** |
| ------- | ------------- | ------- | -------- |
| request | FileUpdateReq | 无       | 文件更新请求类型 |

| **参数名**       | **类型** | **是否必填** | **设置方法**          | **参数描述**                                 |
| ------------- | ------ | -------- | ----------------- | ---------------------------------------- |
| bucket        | String | 是        | 构造函数              | bucket名称                                 |
| cosPath       | String | 是        | 构造函数              | 文件在对象存储服务端的路径                            |
| bizAttr       | string | 否        | setBizAttr()      | 文件属性                                     |
| authority     | string | 否        | setAuthority()    | 文件权限，默认是继承bucket的权限合法取值: eInvalid(继承bucket), eWRPrivate(私有读写), eWPrivateRPublic(私有写, 公有读) |
| forbid        | int    | 否        | setForbid()       | 文件封禁标志                                   |
| custom_header | map    | 否        | setCustomHeader() | 用户自定义属性                                  |

custom_header说明

| **参数名**             | **类型** | **是否必填** | **参数描述**                                 |
| ------------------- | ------ | -------- | ---------------------------------------- |
| Cache-Control       | string | 否        | 参见HTTP的Cache-Control                     |
| Content-Type        | string | 否        | 参见HTTP的Content-Type                      |
| Content-Disposition | string | 否        | 参见HTTP的Content-Language                  |
| Content-Language    | string | 否        | 参见HTTP的Content-Disposition               |
| Content-Encoding    | string | 否        | 参见HTTP的Content-Encoding                  |
| x-cos-meta-         | string | 否        | 自定义HTTP 头，参数必须以x-cos-meta-开头，值由用户定义，可设置多个 |

**tips:** custom_header是整体覆盖式更新，即更新属性可以选择其中的某几个，如果本次只更新其中的某几个，其他的都会被抹掉。

#### 返回结果说明

通过函数返回值返回请求结果的json字符串：

| **参数名** | **类型** | **是否必然返回** | **参数描述**  |
| ------- | ------ | ---------- | --------- |
| code    | Int    | 是          | 返回码，成功时为0 |
| message | String | 是          | 描述信息      |

#### 示例

``` c++
FileUpdateReq fileUpdateReq(bucket,dstpath);
fileUpdateReq.setBizAttr(file_biz_attr);
fileUpdateReq.setAuthority(auth);
fileUpdateReq.setForbid(0);
fileUpdateReq.setCustomHeader(custom_header);
string rsp = cos.FileUpdate(fileUpdateReq);
```

### 文件查询

接口说明：用于文件的查询，可以通过此接口查询文件的各项属性信息。

#### 方法原型

``` c++
string CosAPI::FileStat(FileStatReq& request)
```

#### 参数说明

| **参数名** | **类型**      | **默认值** | **参数描述** |
| ------- | ----------- | ------- | -------- |
| request | FileStatReq | 无       | 目录列表请求类型 |

FileStatReq

| **参数名** | **类型** | **是否必填** | **默认值** | **参数描述**       |
| ------- | ------ | -------- | ------- | -------------- |
| bucket  | String | 是        | 无       | bucket名称       |
| cosPath | String | 是        | 无       | 文件在对象存储服务端的全路径 |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **必然返回** | **参数描述**                   |
| ------- | ------ | -------- | -------------------------- |
| code    | Int    | 是        | 错误码，成功时为0                  |
| message | String | 是        | 错误信息                       |
| data    | Array  | 是        | 返回数据，请参考《Restful API 文件查询》 |

#### 示例

``` c++
FileStatReq fileStatReq(bucket,dstpath);
string rsp = cos.FileStat(fileStatReq);
```

### 文件删除

接口说明：用于文件的删除，可以通过此接口删除已经上传的文件。

#### 方法原型

``` c++
FileDeleteReq fileDeleteReq(bucket,dstpath);
string rsp = cos.FileDelete(fileDeleteReq);
```

#### 参数说明

| **参数名** | **类型**          | **默认值** | **参数描述** |
| ------- | --------------- | ------- | -------- |
| request | FolderDeleteReq | 无       | 目录删除请求类型 |

FolderDeleteReq

| **参数名** | **类型** | **是否必填** | **默认值** | **参数描述**       |
| ------- | ------ | -------- | ------- | -------------- |
| bucket  | String | 是        | 无       | bucket名称       |
| cosPath | String | 是        | 无       | 文件在对象存储服务端的全路径 |

#### 返回结果说明

通过函数返回值返回请求结果的json字符串

| **参数名** | **类型** | **是否必然返回** | **参数描述**  |
| ------- | ------ | ---------- | --------- |
| code    | Int    | 是          | 返回码，成功时为0 |
| message | String | 是          | 描述信息      |

#### 示例

``` c++
FileDeleteReq fileDeleteReq(bucket,dstpath);
string rsp = cos.FileDelete(fileDeleteReq);
```