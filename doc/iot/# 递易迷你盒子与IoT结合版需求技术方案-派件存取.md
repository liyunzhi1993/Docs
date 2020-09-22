# 递易迷你盒子与IoT结合版需求技术方案-派件存取
> 作者：李允智 
> 时间： 2020-09-10
> 版本：V1

## 前言
递易迷你盒子是由智能柜业务复制而出，在之前是智能柜业务通过mqtt和设备进行交互，V1版本要做
-  与设备交互走IoT
-  原先业务方控制格口的权利，转交给IoT
-  IoT增加格口订单
-  IoT增加设备信息-支持设备通过接口上报新增，支持
-  取消预订单，使用延迟队列【已在京东云柜寄件中试验过】

模块仍分为
> - Device 设备模块
> - Box 柜机模块

所有接口返回
>- Code状态码 200为正常，0为错误
>- Message错误提示 当为0时有值

## 延迟队列方案处理消息一致性【替代预订单】
**简要描述：** 

- 统一的消息一致性框架是结合以往与柜机业务流程过于复杂，异常难以处理来升级优化的一种方案
- 最重要的目的是 保证 平台和柜机的消息一致性【即平台发布开箱指令，柜机告知平台开箱成功/失败，或者没告诉平台】
- 脱离了正常业务流程，保证了简单高效

**技术解析：** 
- 使用aop，通过拦截对应方法，如取件![](https://www.showdoc.cc/server/api/common/visitfile/sign/53277b0572aaa98c7336e06a1bc0daf1?showdoc=.jpg)
- BizType【定义业务场景，如取件：0】
- BidIdName【触发业务场景的业务ID，如寄件订单号ID】
- DelaySeconds 【延迟生效秒数】比如取件后，5s之后来检查是否用户正常取件完成

**柜机流程变动：** 
- 原有的异常流程耦合在了正常流程中，比如 取件需要申请取件，然后再确认取件，现在是取件就是取件
- 柜机端需要回传同步消息，告知该笔取件已完成或者失败【如开箱失败】，或不回【如柜机网络异常】，平台来处理回滚操作【即回滚取件操作】


<!-- ## 设计疑问点
- 设备信息，格口信息，新老兼容问题
> 1、设备信息IoT可以在认证的时候为每个新设备从kernel_device_group表同步信息

> 2、格口信息IoT不保留，直到设备和格口供应链推送给IOT，老数据库去同步kernel_smartbox_boxconfig，铁塔是没有格口管理的，只有设备 -->


## Device   
### 1、新增设备【接口】
> 将设备信息推送到IoT，提供给铁塔，内部在认证从老数据库同步

#### 接口请求入参
> 请求地址：Device/Add

| 参数名           | 必选 | 类型   | 说明                 |
| :--------------- | :--- | :----- | -------------------- |
| DeviceSn         | 是   | string | 设备编号             |
| DeviceGroupId         | 是   | int | 设备组Id             |
| MainDeviceId         | 是   | int | 主设备Id             |
| CustomerSN         | 是   | int | 设备组Id             |
| DevicePassword         | 是   | string | 设备密码             |
| ManagePassword         | 是   | string | 管理密码             |
| CurrentVersion         | 是   | string | 当前版本号             |
| SystemType         | 是   | smallint | 系统类型 1 安卓 2ios 3Windows(字典SystemType)             |
| MonitorDeviceSN         | 是   | string | 监控设备编号             |
| DeviceRealStatus         | 是   | smallint | 设备状态(0:不在线,1:在线)             |
| EnableStatus         | 是   | smallint | 启用状态(0:未启用,1:启用;2:停用)             |
| EnableTime         | 是   | int | 启用时间-单位：秒             |
| InitTime         | 是   | int | 首次初始化时间-单位：秒              |
| DisableTime         | 是   | int | 停用时间-单位：秒              |
| DeviceStatusUpTime         | 是   | int | 设备状态更新时间-单位：秒              |
| DeviceInstallStatus         | 是   | smallint | 设备安装调试状态（1待安装  2未完成（待调试）3:安装完成）             |
| DeviceConfigStatus         | 是   | smallint | 设备配置状态（0：未配置，1已配置成功，2配置失败）             |
| Longitude         | 是   | decimal | 经度             |
| Latitude         | 是   | decimal | 纬度             |
| Status         | 是   | smallint | 格口状态（0 未锁定 1已锁定）             |
| DetailAddress         | 是  | string | 详细地址             |



#### 接口请求出参

| 参数名           | 类型    | 说明               |
| :--------------- | :------ | ------------------ |
| Data - IsSuccess | bool    | 新增设备是否成功 |
| Code             | integer | 返回状态码         |
| Message          | string  | 错误提示           |

> **IsSuccess为faslse错误提示信息有**
> - 新增设备失败


#### 表设计

> 表名：Device

| 字段名         | 类型     | 说明     |
| :------------- | :------- | -------- |
| Id             | bigint   | 自增主键 |
| DeviceSn       | varchar  | 设备编号 |
| DevicePassword | varchar  | 设备密码 |
| DeviceGroupId  | int | 设备组Id      |
| MainDeviceId   | int | 主设备Id      |
| CustomerSN     | int | 柜号      |
| ManagePassword | string | 管理密码   |
| CurrentVersion | string | 当前版本号 |
| SystemType     | smallint | 系统类型 1 安卓 2ios 3Windows(字典SystemType) |
| MonitorDeviceSN| string | 监控设备编号 |
| Status              | smallint | 格口状态（0 未锁定 1已锁定）|
| DeviceRealStatus| smallint | 设备状态(0:不在线,1:在线) |
| EnableStatus    | smallint | 启用状态(0:未启用,1:启用;2:停用) |
| DeviceInstallStatu  | smallint | 设备安装调试状态（1待安装  2未完成（待调试）3:安装完成） |
| DeviceConfigStatus  | smallint | 设备配置状态（0：未配置，1已配置成功，2配置失败）  |
| Longitude           | decimal | 经度  |
| Latitude            | decimal | 纬度  |
| CreateTime     | int   | 创建时间-单位：秒 |
| UpdateTime     | int   | 更新时间-单位：秒 |
| EnableTime      | int | 启用时间-单位：秒   |
| InitTime        | int | 首次初始化时间-单位：秒  |
| DisableTime     | int | 停用时间-单位：秒  |
| DeviceStatusUpTime  | int | 设备状态更新时间-单位：秒   |
| DetailAddress       | string | 详细地址 |


> 表名：Equipment_Status

| 字段名         | 类型     | 说明     |
| :------------- | :------- | -------- |
| Id             | bigint   | 自增主键 |
| DeviceSn       | varchar  | 设备编号 |
| TenantId       | int      | 商户Id |
| StationId      | int      | 站点Id |
| DeviceType     | smallint | 设备类型 |
| Cam            | smallint | 摄像头状态(1-正常，0-故障)|
| Scan           | smallint | 扫码器(1-正常，0-故障)|
| LockPlate      | smallint | 锁控板状态(1-正常，0-故障)|
| SD             | decimal  | SD卡可用量 单位M|
| NetWork        | smallint | 网络状态(0无网络、1微弱、 2弱、 3中、 4良好 、5优秀)|
| Temperature    | decimal | 温度|
| CreateTime     | int   | 创建时间-单位：秒 |
| UpdateTime     | int   | 更新时间-单位：秒 |




> 表名：Gekou_Order

| 字段名         | 类型     | 说明    |
| :------------- | :-------| ------- |
| Id             | bigint  | 自增主键 |
| TenantId       | int     | 商户Id  |
| StationId      | int     | 站点Id  |
| MouthType      | int     | 格口类型（）|
| CreateTime     | string   | 创建时间 |
| UpdateTime     | string   | 更新时间 |


## Box 柜机模块

### 1、申请并开启格口接口
> 提供给业务方，申请并开启格口，原业务系统中【获取一个可用格口】，将格口的控制权转交给IoT，IoT来统一分配格口。如果有可用，开启格口

#### 接口请求入参
> 请求地址：Box/ApplyOpenCell

| 参数名           | 必选 | 类型   | 说明                 |
| :--------------- | :--- | :----- | -------------------- |
| DeviceSn         | 是   | string | 设备编号             |
| CellType         | 是   | int    | 格口类型             |
| BizOrderId       | 是   | string | 来源业务单号ID     | 

- 格口类型Code
> - 小格口 1
> - 中格口  2
> - 大格口 3
> - 超大格口 4

#### 接口请求出参

| 参数名           | 类型    | 说明               |
| :--------------- | :------ | ------------------ |
| Data - IsSuccess | bool    | 申请并开启是否成功 |
| Data - CellSn    | string  | 格口编码           |
| Data - CellId    | string  | 格口Id           |
| Data - CellPosition    | int  | 格口位置           |
| Code             | integer | 返回状态码         |
| Message          | string  | 错误提示           |

- 格口位置Code
> - 左 0
> - 右 1
> - 主柜 2【中间的】

> **IsSuccess为faslse错误提示信息有**
> - 暂无可用格口
> - 设备无响应
> - 格口开启失败

> 申请完之后IoT会生成格口订单


### 2、 开启格口接口    
> 提供给业务方，开启格口。该接口需要对应业务场景进行格口开启

> 业务场景分为
> - 取件开箱 1
> - 管理员退柜/回收开箱 2
> - 仅开箱 3

> **注意**
> - IoT在开箱成功之后会将格口改为正常可用状态

#### 接口请求入参
> 请求地址：Box/OpenCell

| 参数名       | 必选 | 类型   | 说明         |
| :----------- | :--- | :----- | ------------ |
| DeviceSn     | 是   | string | 设备编号     |
| CellSn       | 是   | string | 格口编码     |
| CellId      | 是   | string | 格口Id     |
| BizSceneType | 是   | int    | 业务场景类型 |
| BizOrderId   | 是   | string | 来源业务单号ID   |

> 第三方业务单号ID，如果没有可传运单号，目的是为了保证，开启的时候格口号和业务单号Id一致才能开

#### 接口请求出参

| 参数名           | 类型    | 说明         |
| :--------------- | :------ | ------------ |
| Data - IsSuccess | bool    | 开启是否成功 |
| Code             | integer | 返回状态码   |
| Message          | string  | 错误提示     |

> **IsSuccess为true正确提示信息为**
> - 格口开启成功

> **IsSuccess为false错误提示信息有**
> - 设备无响应
> - 格口开启失败

#### 格口开启失败，设为故障逻辑

> 3次设备返回【格口开启失败】则将格口设为故障，该故障目前IoT会设置，也会将业务方的格口状态设为故障【临时方案，直到所有格口控制权转交IoT】
> 该格口再3次之前开启成功，则清除标记次数；

### 3、获取格口数量

> 提供给业务方，获取各种类型格口的【可用】格口数量

- 格口类型Code
> - 小格口 1
> - 中格口  2
> - 大格口 3
> - 超大格口 4

#### 接口请求入参
> 请求地址：Box/QueryCellCount

| 参数名       | 必选 | 类型   | 说明         |
| :----------- | :--- | :----- | ------------ |
| DeviceSn     | 是   | string | 设备编号     |


#### 接口请求出参

| 参数名           | 类型    | 说明         |
| :--------------- | :------ | ------------ |
| Data - SmallCount | int    | 小格口可用数量 |
| Data - CenterCount | int    | 中格口可用数量 |
| Data - BigCount | int    | 大格口可用数量 |
| Data - LargeCount | int    | 超大格口可用数量 |
| Code             | integer | 返回状态码   |
| Message          | string  | 错误提示     |

### 4、格口状态(开启/关闭)

> 提供给业务方，获取设备格口的格口状态

#### 接口请求入参
> 请求地址：Box/QueryCellStatus

| 参数名       | 必选 | 类型   | 说明         |
| :----------- | :--- | :----- | ------------ |
| DeviceSn     | 是   | string | 设备编号     |
| CellSn     | 是   | string | 设备编号     |
| BizOrderId   | 是   | string | 来源业务单号ID   |

> 第三方业务单号ID，如果没有可传运单号，目的是为了保证，判断格口号和业务单号Id一致才会做判断

#### 接口请求出参

| 参数名           | 类型    | 说明         |
| :--------------- | :------ | ------------ |
| Data - CellStatus | int    | 开启还是关闭 |
| Code             | integer | 返回状态码   |
| Message          | string  | 错误提示     |

- 格口状态CellStatus
>- 开启 1
>- 关闭 0

### 5、完成格口

> 提供给业务方，在用户场景为主动使用完格口时调用，如  拒收 场景等，目的为了让格口解锁，回归正常状态

#### 接口请求入参
> 请求地址：Box/FinishCell

| 参数名       | 必选 | 类型   | 说明         |
| :----------- | :--- | :----- | ------------ |
| DeviceSn     | 是   | string | 设备编号     |
| CellSn     | 是   | string | 设备编号     |
| BizOrderId   | 是   | string | 来源业务单号ID   |

> 第三方业务单号ID，如果没有可传运单号，目的是为了保证，判断格口号和业务单号Id一致才会做处理

#### 接口请求出参

| 参数名           | 类型    | 说明         |
| :--------------- | :------ | ------------ |
| Data - IsSuccess | bool    | 完成格口操作成功还是失败 |
| Code             | integer | 返回状态码   |
| Message          | string  | 错误提示     |




