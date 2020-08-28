# 递易物联网平台http请求Mqtt协议接口
> 作者：李允智 
> 时间： 2020-08-14
> 版本：1.1

## Http请求Mqtt协议接口
> 请求地址 Api/PublishMessage
### 接口请求入参

|参数名|必选|类型|说明|
|:----|:---|:-----|-----|
| Topic|是|string|发布的Topic  |
| Content|是|string|发布的消息体，需要Base64字符串  |
| ReceiveTopic|否|string|接收消息Topic【只有不为空，才会去处理接收设备端mqtt的回复消息】  |
| DeviceSn|是|string|设备编号  |

> Content只需要Base64字符串就好 ，至于递易体系下的**DyIot#字符串长度**前缀，IoT会自动处理

### 接口请求出参

|参数名|类型|说明|
|:-----|:-----|-----|
|Data - Content|string|回复消息的内容  |
|Data - ReceiveStatus|integer|回复状态【1 已回复，0 未回复 即超时】  |
| Code|integer|返回状态码【200正常，0有错误】  |
| Message|string|错误描述，Code为0使用  |

> Code 为0时 ，返回Message消息有



### 接口请求示例

#### 1、在Startup中将HttpClient注入IOC
```csharp
services.AddHttpClient("iotmqtt", x =>
{
    //可配置阿波罗
    x.BaseAddress= new Uri("http://iotmqtt-test.diyibox.com");
});
```
#### 2、调用
```csharp
//递易mqtt消息内容处理，规则仍然不变
var mqttData = GetSmartboxMqttData(request.AccountID, request.DeviceGroupSN, request.DeviceType);
mqttData.Cid = "7007" + request.DeviceGroupSN;

//请求mqtt的消息体
var content = new
{
    Met = "Reboot",
    Con = new
    {
        Mid = mqttData.Mid,
        Ts = mqttData.Ts,
        Sign = mqttData.Sign,
        Cid = mqttData.Cid
    }
};

//请求入参模型
var iotPublishMessageModel = new
{
    //请求Topic，目前递易体系的Topic定义请结合自身项目
    Topic = "000"+request.DeviceType+request.DeviceGroupSN+"/DeliveryFoodBase",
    //请求mqtt的消息体
    Content = EncryptUtil.Base64Encode(JsonConvert.SerializeObject(content)),
    //消息ID
    MessageId = mqttData.Mid,
    //可选参数 需要消息回复则传，目前递易体系的回复Topic定义请结合自身项目
    ReceiveTopic = "7007" + request.DeviceGroupSN + "/Restart",
    //设备号
    DeviceSn = request.DeviceGroupSN
};

//
var client = _httpClientFactory.CreateClient("iotmqtt");
var httpResponseMessage = client.PostAsJsonAsync("Api/PublishMessage", iotPublishMessageModel).Result;
//如果当前方法用async异步修饰，请不要.Result 请await
//var httpResponseMessage =await client.PostAsJsonAsync("Api/PublishMessage", iotPublishMessageModel);
if (httpResponseMessage.IsSuccessStatusCode)
{
    //成功，返回内容自己自行解析，解析参考接口请求出参
    var data= httpResponseMessage.Content.ReadAsStringAsync().Result;
    //如果当前方法用async异步修饰，请不要.Result 请await
   //var data= await httpResponseMessage.Content.ReadAsStringAsync();
}
else
{
    //这里是失败的代码了，即发送失败了
}
```

## 示例项目代码参考
> 由于送餐柜提前进行对接，在项目deliveryfood，分支master_iot_lyz20200730中SmartBoxController/IoTRebootDeviceTest 有示例代码

## 20200814增加逻辑处理
- 增加了判断设备状态，如果设备不在线，则提示
> - Message为设备网络异常

- 增加了 设备回传消息失败的情况下 60s内连续10次失败，则返回提示
> - Message为设备异常，请稍后再试
> - 如果10次内有成功，则重新从0开始计算