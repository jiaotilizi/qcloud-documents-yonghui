您可以通过云平台数据库迁移工具将业务数据迁移到CDB for TDSQL中，迁移工具可以把同地域下不同类型的源数据库的数据迁移到TDSQL中，目前支持的源库类型有：
1. 源数据库来自在云平台基础网络中云服务自建的MySQL实例（即将支持[VPC网络](/doc/product/215/%E4%BA%A7%E5%93%81%E6%A6%82%E8%BF%B0)）；
2. 源数据库在云平台的CDB for MySQL实例；
3. 源数据库在另外一个CDB for TDSQL实例；

**迁移步骤如下：**
### 1.1. 进入迁移工具页面
![](//mccdn.qcloud.com/img56835f031e53b.png)

### 1.2. 创建迁移任务
![](//mccdn.qcloud.com/img56835f3f5fe77.png)
点击创建迁移任务，输入任务名称、源库和目标TDSQL的信息。

![](//mccdn.qcloud.com/img56835f611f583.png)
然后选择要迁移的数据库，创建并检查迁移任务信息。

![](//mccdn.qcloud.com/img56835f91aec32.png)
**数据迁移**：将选中数据库中的数据导出，然后在TDSQL中导入。
**增量同步**：在进行数据导出导入后，设置TDSQL为源库的备库，进行主备增量同步。

### 1.3. 校验迁移任务信息
在创建完迁移任务后，您需要对迁移任务信息进行校验，只有所有校验项通过后才能启动迁移任务。任务校验存在3种状态：
- 通过：表示校验完全通过
- 警告：表示校验不通过，迁移过程中或迁移后可能影响数据库正常运行但不影响迁移任务的执行。
- 失败：表示校验不通过，无法进行迁移。如果校验失败，请根据出错的校验项，检查并修改迁移任务信息，然后重试校验。失败原因请参考：“校验失败说明”

![](//mccdn.qcloud.com/img56837a4d5ead6.png)

### 1.4. 启动迁移
在校验通过后，您可以启动迁移任务，如果您设定了迁移任务的定时时间，则迁移任务会在设定的时间开始排队并执行，如果没有设置定时任务，则迁移任务会立即执行。
迁移启动后，您可以在迁移任务下看到对应的迁移进度信息。
![](//mccdn.qcloud.com/img56837a6d2a476.png)
<span style="background-color:#FFFF00">注：由于系统设计限制，一次性提交或排队多个迁移任务将按排队时间串行执行。</span>

### 1.5. 增量同步
如果您在创建迁移任务时选择了增量同步选项，那么数据迁移完成后，我们会把目标TDSQL库设置成源数据库的备库，通过主备同步来把迁移过程中源库的新增的数据同步到目标TDSQL库中。此时，源库上的修改都会同步到目标TDSQL中，你可以把业务切换到目标TDSQL上，然后点击完成迁移。
点击完成迁移后，主备同步关系会断开。

### 1.6. 中止迁移
在迁移过程中，如果您需要停止迁移，可以点击中止按钮进行中止。
![](//mccdn.qcloud.com/img56837a93c69e0.png)
<span style="background-color:#FFFF00">注意：再次启动可能导致校验失败或任务失败，您可能需要手动清空目标库内的数据，才能再次启动迁移任务。</span>

## 2.通过第三方工具迁入
如果您的源库不在云平台上或者在VPC网络环境内，那么您可以通过第三方工具来先把源库的数据导出，然后上传到云平台服务器上，再把数据导入到TDSQL。
常见的导入导出工具有：mysqldump、mydumper、MySQL GUI Tools等。
