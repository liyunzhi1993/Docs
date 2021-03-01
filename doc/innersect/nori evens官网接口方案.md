 # nori evens官网接口方案
> 作者：李允智 
> 时间： 2021-02-01
> 版本：1.1

## 前言
在DmcKal.com的基础上复制，复制之后为通用WebShop，按照ShopID来区分，接口在dmc的基础，原dmc接口都为shopid为40，增加nori evens的ShopID为51。

> 注：即使无需改造的接口也需要验证逻辑是否正常

## 操作记录【便于后期发布计划】

> 增加了两张表webshoporder webshoporderitem
> 数据库metadata表中增加记录【记录新增的表】
> 增加webshopshopproduct视图

## Account 会员模块

> 短信&&登录注册和InnersectApp通用

## Address 地址模块

> 和InnersectApp通用


## Eshop商城 外币

### 1、获取外币与汇率
> 请求地址：https://m3.innersect.net/api/eshop/shopcurrunits/search?word=41

当前只有一条记录，为"40	USD	$	7.1300" 如果新增外币数据该接口可能需要改造！也需要检查innsersectApp是否也用到该接口

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

## Eshop商城 订单

### 1、创建订单
> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/shoppingorder/create

该接口的ShopID，来源于订单明细ShoppingItem的ShopID，但是要确定前端传参的shopId，是否正确


### 2、提交订单
> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/submit

该接口的ShopID，来源于创建订单的缓存

### 3、计算运费
> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/calcfreight

需要改造，增加外币，可能影响了计算逻辑

### 4、获取订单
> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/{id}

获取订单，通过id获取，无需更改

### 5、获取订单列表

> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/list

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 6、获取未支付订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/unpayed

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 6、获取已支付订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/payed

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |


### 6、获取已答复订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/received

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 7、获取已取消订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/canceled

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 8、取消订单

> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/cancel/{orderId}

无需改造，仅仅更改订单状态为已取消

### 9、获取所有票据订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/allTicketOrders

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |


### 10、获取所有未支付票据订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/unpayedTicketOrders

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 11、获取所有支付票据订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/payedTicketOrders

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 12、获取所有答复票据订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/receivedTicketOrders

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |


### 13、获取所有取消票据订单列表

> 请求地址：https://m3.innersect.net/api/eshop/ebshoporders/{shopID}/canceledTicketOrders

需要改造，增加shopid

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

## Eshop商城 购物车

### 1、添加购物车
> 请求地址：https://m3.innersect.net/api/eshop/webshopshopingcart/add

该接口的ShopID，来源于订单明细ShoppingItem的ShopID，但是要确定前端传参的shopId，是否正确

### 2、更新购物车
> 请求地址：https://m3.innersect.net/api/eshop/webshopshopingcart/update

该接口的ShopID，来源于订单明细ShoppingItem的ShopID，但是要确定前端传参的shopId，是否正确

### 3、移除购物车
> 请求地址：https://m3.innersect.net/api/eshop/webshopshopingcart/remove

该接口的ShopID，来源于订单明细ShoppingItem的ShopID，但是要确定前端传参的shopId，是否正确

### 4、批量移除购物车
> 请求地址：https://m3.innersect.net/api/eshop/webshopshopingcart/remove/list

该接口的ShopID，来源于订单明细ShoppingItem的ShopID，但是要确定前端传参的shopId，是否正确

### 5、我的购物车

> 请求地址：https://m3.innersect.net/api/eshop/webshoporders/{shopID}/my

需要改造，增加shopid来区分是哪个购物车，以前的逻辑是写死的就是dmc

#### 增加接口请求入参

| 参数名   | 必选 | 类型   | 说明     |
| :------- | :--- | :----- | -------- |
| ShopId | 否   | Int | 商城ID，默认为40（dmc商城） |

### 6、货币转换

> 请求地址：https://m3.innersect.net/api/eshop/webshopshopingcart/my/converted

可能需要改造，新增了新的货币，需要验证

### 7、购物车同步

> 请求地址：https://m3.innersect.net/api/eshop/webshopshopingcart/sync

该接口的ShopID，来源于订单明细ShoppingItem的ShopID，但是要确定前端传参的shopId，是否正确

## Eshop商城 商品

> 改模块新增了货币可能会有影响，需要验证

### 1、获取所有商品

> 请求地址：https://m3.innersect.net/api/eshop/{currUnit}/{shop}/webShopProducts/getAll


需要改造，变更shop参数，需要验证，以前的逻辑是写死的40（dmc商城)


### 2、搜索商品

> 请求地址：https://m3.innersect.net/api/eshop/{currUnit}/{shop}/webShopProducts/search

需要改造，变更shop参数，需要验证，以前的逻辑是写死的40（dmc商城)

### 3、高级过滤商品

> 请求地址：https://m3.innersect.net/api/eshop/{currUnit}/{shop}/webShopProducts/filterBy

需要改造，变更shop参数，需要验证，以前的逻辑是写死的40（dmc商城)

### 4、获取商品详情

> 请求地址：https://m3.innersect.net/api/eshop/{currUnit}/{shop}/webShopProducts/{prodId}

需要改造，变更shop参数，需要验证，以前的逻辑是写死的40（dmc商城)

### 4、获取商品详情图

> 请求地址：https://m3.innersect.net/api/eshop/{currUnit}/{shop}/webShopProducts/{prodID}/detail

无需改造

### 获取商品尺码图

> 请求地址：https://m3.innersect.net/api/eshop/{currUnit}/{shop}/webShopProducts/{prodID}/sizeBase

无需改造




