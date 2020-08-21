# 递易平台IoT与柜机MQTT协议
> 作者：李允智 
> 时间： 2020-08-21
> 版本：1.0

## 前言
在原有与柜机MQTT协议中，参与了业务的逻辑的处理，而IoT主要目的是为了解耦，所以需要重新定义新的MQTT协议

## Topic

- 柜机订阅：Topic统一定为 000设备类型+设备号/IoTSmartBase  例如：智能柜设备类型为2 柜机订阅：0002设备号/IoTSmartBase
- 平台订阅：Topic统一定为Cid/IoTBase Cid为平台下发指令的Cid

## Met List 请求方法列表

-  打开格口 OpenCell
-  格口状态 CellSta

## 协议

### 1、打开格口 OpenCell
> 开启指定格口

#### 平台下发，柜机订阅
> Met：OpenCell

| 参数名           | 必选 | 类型   | 说明                 |
| :--------------- | :--- | :----- | -------------------- |
| Dt     | 是   | string | 设备类型     |
| Sn     | 是   | string | 设备编号     |
| Mid   | 是   | string | 消息ID   |
| Ts   | 是   | string | 时间戳   |
| Sign   | 是   | string | 签名   |
| Cid   | 是   | string | ClientId   |
| CellSn  | 是   | string | 格口编码   |

#### 柜机下发，平台订阅

| 参数名           | 类型    | 说明               |
| :--------------- | :------ | ------------------ |
| Code             | integer | 返回状态码         |
| Msg          | string  | 错误提示           |
| Dt     | 是   | string | 设备类型     |
| Sn     | 是   | string | 设备编号     |
| Mid   | 是   | string | 消息ID   |
| Ts   | 是   | string | 时间戳   |
| Sign   | 是   | string | 签名   |
| Cid   | 是   | string | ClientId   |

> Code返回状态码
> - 200 打开格口成功
> - 201 打开格口失败

## 协议

### 1、格口状态检查 CellSta
> 查询指定格口是关闭还是开启状态

#### 平台下发，柜机订阅
> Met：CellSta

| 参数名           | 必选 | 类型   | 说明                 |
| :--------------- | :--- | :----- | -------------------- |
| Dt     | 是   | string | 设备类型     |
| Sn     | 是   | string | 设备编号     |
| Mid   | 是   | string | 消息ID   |
| Ts   | 是   | string | 时间戳   |
| Sign   | 是   | string | 签名   |
| Cid   | 是   | string | ClientId   |
| CellSn  | 是   | string | 格口编码   |

#### 柜机下发，平台订阅

| 参数名           | 类型    | 说明               |
| :--------------- | :------ | ------------------ |
| Code             | integer | 返回状态码         |
| Msg          | string  | 错误提示           |
| Dt     | 是   | string | 设备类型     |
| Sn     | 是   | string | 设备编号     |
| Mid   | 是   | string | 消息ID   |
| Ts   | 是   | string | 时间戳   |
| Sign   | 是   | string | 签名   |
| Cid   | 是   | string | ClientId   |

- Code返回状态码
>- 200 关闭
>- 201 开启