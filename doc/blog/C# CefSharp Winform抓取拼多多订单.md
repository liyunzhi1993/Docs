# C# CefSharp Winform抓取拼多多订单

## 前言
> - 只支持查询自己的买家订单
> - 支持Excel导出，只支持部分字段
> - 纯技术实战分享

## 如何使用
- 直接运行项目
- 登录拼多多
- 点击全部订单
- 等待页面加载不跳转了，点击 导出订单按钮

## 登录验证
基于CefSharp Winform加载Web浏览器（chrome内核），浏览器地址导航到http://mobile.yangkeduo.com/personal.html，然后用手机验证码方式登录

## 获取订单数据
```csharp
 if (request.Method.Equals("POST")&&request.Url.Contains("order_list"))
{
    var filter = FilterManager.GetFileter(request.Identifier.ToString()) as TestJsonFilter;
    if (filter==null)
    {
        return;
    }
    ASCIIEncoding encoding = new ASCIIEncoding();
    //这里截获返回的数据
    var data = encoding.GetString(filter.DataAll.ToArray());
}
 ```
 > - 截取到浏览器发送的**POST**请求并且URL地址里包含**order_list**的
 > - 得到url地址为：http://mobile.yangkeduo.com/proxy/api/api/aristotle/order_list?pdduid=你的用户ID
 >- 得到post数据为json

 **但是由于采用了分页获取的仍然不是全部，这样我们必须通过手动模拟Post请求来加载全部**

 ### 尝试抓取Post数据
 > 将byte转为string之后得到json
 ```json
 {"timeout":1300,"type":"all","page":1,"pay_channel_list":["9","30","31","35","38","52","97","122","135","322","-1"],"origin_host_name":"mobile.yangkeduo.com","size":10,"offset":"200725-031832966363189"}
 ```

 看得出size为10条数据 所以尝试更改post入参试试，offset应该是分页的最后条记录作为偏移量，理论我们可以传空试试，从第一条记录消费

> 结论是拼多多那边做了验证 返回了errorCode 300000

**重新梳理！**
#### 获取Post Json
```csharp
Encoding.Default.GetString(request.PostData.Elements[0].Bytes);将Byte转为String
```
- 第一页json
 ```json
{"timeout":1300,"type":"all","page":1,"pay_channel_list":["9","30","31","35","38","52","97","122","135","322","-1"],"origin_host_name":"mobile.yangkeduo.com","size":10,"offset":"200803-173139887143189"}
```
- 第二页 
 ```json
{"timeout":1300,"type":"all","page":1,"pay_channel_list":["9","30","31","35","38","52","97","122","135","322","-1"],"origin_host_name":"mobile.yangkeduo.com","size":10,"offset":"200727-071906181943189"}
```
- 第三页
```json
{"timeout":1300,"type":"all","page":1,"pay_channel_list":["9","30","31","35","38","52","97","122","135","322","-1"],"origin_host_name":"mobile.yangkeduo.com","size":10,"offset":"200725-031832966363189"}
```

#### 区别的只有offset，那么offset是啥
由于offset是前端请求必然是在后端返回的订单数据里有，所以拿到offset值去搜索，可以找到为订单order_sn字段！

那么下一步的思路清晰了，解析返回的json

#### 解析返回json
- 根据json建立model
```csharp
public class Order
{
    public long server_time { get; set; }
    public List<OrderItem> orders { get; set; }
}
public class OrderItem { 
    /// <summary>
    /// 快递单号
    /// </summary>
    public string tracking_number { get; set; }
    /// <summary>
    /// 订单编码
    /// </summary>
    public string order_sn { get; set; }
    /// <summary>
    /// 快递公司ID
    /// </summary>
    public string shipping_id { get; set; }
    /// <summary>
    /// 订单状态 4为已收货
    /// </summary>
    public int status { get; set; }
    /// <summary>
    /// 订单金额
    /// </summary>
    public int display_amount { get; set; }
}
```
- 获取返回json，拿到最后一个的订单编码然后重新请求，直到订单查完
```csharp
if (request.Method.Equals("POST")&&request.Url.Contains("order_list"))
{
    var filter = FilterManager.GetFileter(request.Identifier.ToString()) as TestJsonFilter;
    if (filter==null)
    {
        return;
    }
    ASCIIEncoding encoding = new ASCIIEncoding();
    //这里截获返回的数据
    var data = encoding.GetString(filter.DataAll.ToArray());
    var orderList= JsonConvert.DeserializeObject<Order>(data);
    if (orderList.orders!=null&& orderList.orders.Count!=0)
    {
        Thread.Sleep(1000);
        FilterManager.AddOrder(orderList);
        //反向排序
        orderList.orders.Reverse();
        //获取第一个
        var order=orderList.orders.FirstOrDefault();
        string json = "{\"timeout\":1300,\"type\":\"all\",\"page\":1,\"pay_channel_list\":[\"9\",\"30\",\"31\",\"35\",\"38\",\"52\",\"97\",\"122\",\"135\",\"322\",\"-1\"],\"origin_host_name\":\"mobile.yangkeduo.com\",\"size\":10,\"offset\":\"#offset#\"}";
        json=json.Replace("#offset#", order.order_sn);
        chromiumWebBrowser.Navigate(Encoding.Default.GetBytes(json), request);
    }
}
```

#### 导出Excel
> 引用NOPI进行导出，将List转为DataTable

#### 演示效果
> ![企业微信截图_15972252751830](https://raw.githubusercontent.com/liyunzhi1993/Image/master/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15972252751830.png)

##  开源地址
[点我前往github](https://github.com/liyunzhi1993/Pinduoduo)


