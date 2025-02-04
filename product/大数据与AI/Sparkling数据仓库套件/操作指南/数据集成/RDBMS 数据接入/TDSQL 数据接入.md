﻿## 操作场景
云数据仓库套件 Sparkling 支持多样化的数据接入方式和数据源管理。数据接入方式包括：
- 关系型数据库（RDBMS）接入：可以通过 RDBMS 数据接入方式将云数据库 MySQL 、分布式数据库TDSQL中的数据接入到 Sparkling 中。
- 腾讯云对象存储（COS）数据接入：可以通过生成账户密钥，建立存储桶（bucket）的方式进行 COS 数据接入。
- 腾讯云消息队列（CKafka）数据接入：可以通过 Kafka 数据接入方式将腾讯云 CKafka 中的数据接入 Sparkling 中。

本节为您介绍分布式数据库TDSQL 数据接入方法。通过 RDBMS 数据接入功能可以实现将分布式数据库TDSQL 中的数据以全量或增量的方式导入 Sparkling 中。用户可以通过时间戳增量追加方式实现增量同步，通过自定义调度周期实现定时数据接入功能。
更多关于云数据库 TDSQL 的信息请参见 [分布式数据库TDSQL 产品介绍](https://cloud.tencent.com/document/product/557)。

## 操作步骤
登录 [Sparkling 控制台](https://sparkling.cloud.tencent.com)，在左侧导航单击【数据】进入数据接入页面，按以下操作步骤完成 RDBMS 数据接入：
![](https://main.qcloudimg.com/raw/0538fd46bbe73c479c1f058a3b9936d6.png)


### 1. 数据源配置
选择【RDBMS】数据类型，支持【新建数据源】和【接入已有数据源】两种数据源接入方式。
- **新建数据源方式**
a. 接入方式选择【新建数据源】。
b. 填写分布式数据库实例 ID（可在 [分布式数据库控制台](https://console.cloud.tencent.com/dcdb) 获取数据库实例 ID）及数据库购买者 UIN（实例购买者在 [账号信息](https://console.cloud.tencent.com/developer) 中的账号 ID）。
c. 单击角色服务授权，授权 Sparkling 服务访问其他相应服务。
	![](https://main.qcloudimg.com/raw/9873f94c2fcfa8f6530a29a125fce9b6.png)
	 
d. 填写要接入的数据表所在的【用户名】、【密码】、【数据库名】、【表名】。
e. 单击【测试连通性】确认是否可以连接到要接入数据表所在的数据库。
f. 待显示【数据连通正常】后，单击【下一步】完成数据源配置操作，同时可以选择【保存数据源】选项将该数据源保存为已有数据源，待下次接入该数据源时可采用【接入已有数据源】方式。
![](https://main.qcloudimg.com/raw/8c12bc4a91195e95997b00556261f979.png)
- **接入已有数据源方式**
   a. 接入方式选择【接入已有数据源】。
	 b. 选择历史保存的已有数据源。
	 c. 输入表名后，单击【下一步】完成数据源配置操作。
![](https://main.qcloudimg.com/raw/35da099d335275b6374868c98e0def82.png)

### 2. 数据预览
在【数据预览】页可以预览数据表中的字段信息，默认抽取五行数据进行预览，预览无误后单击【下一步】。
![](https://main.qcloudimg.com/raw/fee603e23a76c80dc708aa55b39a1e25.png)

### 3. 目标配置
支持【新建表】和【导入已有目标表】两种方式。

- **新建表方式**
a. 选择【新建表】并填写【标题】和【描述】。
b. 字段定义及分区部分可以设置字段名、描述及字段顺序，支持字段裁剪（可通过删除字段实现仅导入部分字段功能）。其中第一列“pt”列为默认分区字段，不支持删除操作，默认以该时间戳作为分区值。
c. 选择格式类型，支持【ORCFILE】和【PARQUET】两种格式。
d. 确认无误后，单击【下一步】完成新建表方式目标配置操作。
![](https://main.qcloudimg.com/raw/bb60ed9754cbd09a87c253623879e15d.png)

- **导入已有目标表方式**
导入已有目标表方式需要集群中已经包含该目标表，其中目标表包括数据导入时创建的表及在工作区中自建的表。
a. 选择【导入已有目标表】后选择目标表名。
b. 设置目标分区。选择【依据例行任务导入动态分区】将会根据您的例行任务配置信息写入对应的分区文件；选择【自定义分区】请按照分区字段明确分区命名。
c. 单击【字段映射】进行字段匹配。若【下一步】处于置灰状态，说明字段映射未成功，请确认目标表与待导入数据源表字段可以匹配。
d. 确认无误后，单击【下一步】完成导入已有目标表方式目标配置操作。
>?使用导入已有目标表方式的前提是已经建立了可以与新导入数据实现字段映射的数据表。
> 
![](https://main.qcloudimg.com/raw/47985deb71399fc2bcb9369dd0fba46e.png)

### 4. 抽取任务配置
目前支持【单次】和【例行】调度方式。

- **单次方式**
a. 调度周期选择【单次】。
b. 数据加载方式可选择【时间戳增量追加】或【整表全量导入】。选择【时间戳增量追加】后需增加过滤条件，该方式只导入符合该过滤条件所包含的时间范围的数据，具体可参看语法说明；选择【整表全量导入】后可选择【写入前清理已有数据(Insert Overwrite)】或【写入前保留已有数据(Insert)】。
>? 时间戳增量追加方式仅适用于导入数据中包含时间戳的情形，若数据中不存在时间戳形式的列，该选项将无法选中。
> 
c. 确认无误后，单击【下一步】完成单次抽取任务配置操作。
![](https://main.qcloudimg.com/raw/aa0e7ee4f2d6365d36fd07003b7c87cc.png)

- **例行方式**
a. 调度周期选择【例行】。
b. 设置间隔周期，支持每天、每周、每月、每小时，例如选择：每天0时0分，即每日0点0分自动开始执行任务。
c. 设置例行时长，即任务持续时间，支持一周、一个月、三个月、一年、永久。
d. 数据加载方式可选择【时间戳增量追加】或【整表全量导入】。选择【时间戳增量追加】后需增加过滤条件，该方式只导入符合该过滤条件所包含的时间范围的数据，具体可参看语法说明；选择【整表全量导入】后可选择【写入前清理已有数据(Insert Overwrite)】或【写入前保留已有数据(Insert)】。
>?时间戳增量追加方式仅适用于导入数据中包含时间戳的情形，若数据中不存在时间戳形式的列，该选项将无法选中。
> 
e. 确认无误后，单击【下一步】完成例行抽取任务配置操作。
![](https://main.qcloudimg.com/raw/e2ad8e695db93a442512cc8ed76858a4.png)
### 5. 预览
在【预览】页可以查看当前设置的数据源信息、数据预览信息、目标表信息及任务调度信息。
![](https://main.qcloudimg.com/raw/6063d3bdf9ad5eff505edf2dfe7a687a.png)
确认无误后单击【完成】即可完成 RDBMS 数据接入任务设置。
