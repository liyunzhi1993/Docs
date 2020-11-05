# C# async await和Spring WebFlux异步编程的区别
> 作者：李允智 
> 时间：2020-11-04
> 版本：1.0

## 在高并发请求下异步非阻塞和同步阻塞的区别
> 使用net core 3.1 webapi和java spring boot创建不同项目，流程一致。通过JMeter模拟并发请求来查看区别

### 1、使用net core 3.1 webapi创建项目提供一个异步非阻塞接口

#### 代码如下
```csharp
[ApiController]
[Route("[controller]/[action]")]
public class WeatherForecastController : ControllerBase
{
    //注入实例
    private readonly WeatherForecast _weatherForecast;
    public WeatherForecastController(WeatherForecast weatherForecast)
    {
        _weatherForecast=weatherForecast;
    }

    //异步非阻塞接口
    [HttpGet]
    public async Task<string> Run()
    {
        return await _weatherForecast.Test();
    }
}
```
```csharp
public class WeatherForecast
{
    public async Task<string> Test()
    {
        //休眠1秒【模拟为http请求外部系统1秒】
        Thread.Sleep(1000);
        return await Task.FromResult("我勒个去");
    }
}
```
#### 2、JMeter结果
> 50个线程 持续一分钟


### 2、使用net core 3.1 webapi创建项目提供一个同步阻塞接口

#### 代码如下
```csharp
[ApiController]
[Route("[controller]/[action]")]
public class WeatherForecastController : ControllerBase
{
    //注入实例
    private readonly WeatherForecast _weatherForecast;
    public WeatherForecastController(WeatherForecast weatherForecast)
    {
        _weatherForecast=weatherForecast;
    }

    //同步阻塞接口
    [HttpGet]
    public string Run()
    {
        return _weatherForecast.Test();
    }
}
```
```csharp
public class WeatherForecast
{
    public string Test()
    {
        Thread.Sleep(1000);
        return "我勒个去";
    }
}
```
#### 2、JMeter结果
> 50个线程 持续一分钟
![企业微信截图_16045016502537](https://raw.githubusercontent.com/liyunzhi1993/Image/master/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_16045016502537.png)

操作步骤

- 1、填写晨阳分配的密码
- 2、输入p 回车显示分配给你的主机
- 3、输入序号 如 1 进入你拥有权限的linux系统

## 3、发布dotnet core程序【正常发布】

操作步骤
- 1、进入系统之后，左侧有Default目录 一直点击进入，进到最底层你存发布文件的位置
- 2、在本地应用程序目录使用dotnet publish -c release 命令打包发布程序 
- 3、如果发布的是灰度需要改两个文件，nlog.config和appsettings.json
> - nlog.config uri灰度更改为：http://172.17.0.9:2001,http://172.17.0.24:2000,http://172.17.0.32:2002
> - nlog.config index灰度更改为 uat-你的项目组简称-项目组业务-\${date:format=yyyyMM}  如：其他柜机项目组 uat-othercabinet-syyw-${date:format=yyyyMM}  
> - appsettings.json apllo配置更改 WMetaServer为"MetaServer": "http://172.17.0.5:6080/" 
> - appsettings.json apllo配置更改 MetaServer为"MetaServer": "http://172.17.0.5:6080"
- 4、将所有发布文件拖到linux文件夹中

- 5、进行发布 dotnet DiYi.ClothingCabinetApi.dll urls=http://0.0.0.0:12311 
> 0.0.0.0 的IP是晨阳要求
> 12311端口是晨阳要求分配的端口的区间

- 6、这样就算发布成功了
> ![企业微信截图_1599014279383](https://raw.githubusercontent.com/liyunzhi1993/Image/master/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_1599014279383.png)
- 7、告知晨阳端口和你的域名绑定，绑定上之后上域名地址查看swagger是否正常，调用一个接口尝试，能不能通，通常都是配置问题导致，如apollo配置错误等，如果没问题
- 8、ctrl+c 关闭dotnet core应用程序，然后nohup dotnet DiYi.ClothingCabinetApi.dll urls=http://0.0.0.0:12311  nohup的指令是后台运行程序，这样发布就结束了

## 4、排错和重新发布【正常发布】

### 排错
- 通过控制台日志来看 是否有报错了
- 最主要的是  你有发布计划，数据库 apollo都配好了，那么理论上是不会有问题的

### 重新发布
- 1、重新发布需要杀死原来的进程，否则端口会被占用 由于我们的进行是通过nohup后台运行的，首先要查到进程ID
> 终端输入：ps -ef 找到你端口的应用 找到进程ID
- 2、杀死应用  终端输入：kill -9 进程ID
- 3、cd到你项目目录的文件 重新nohup dotnet DiYi.ClothingCabinetApi.dll urls=http://0.0.0.0:12311即可

## 5、发布dotnet core程序【晨阳脚本发布】【推荐！】

操作步骤
- 1、进入系统之后，左侧有Default目录 一直点击进入，进到最底层你存发布文件的位置
- 2、在本地应用程序目录使用dotnet publish -c release 命令打包发布程序 
- 3、如果发布的是灰度需要改两个文件，nlog.config和appsettings.json
> - nlog.config uri灰度更改为：http://172.17.0.9:2001,http://172.17.0.24:2000,http://172.17.0.32:2002
> - nlog.config index灰度更改为 uat-你的项目组简称-项目组业务-\${date:format=yyyyMM}  如：其他柜机项目组 uat-othercabinet-syyw-${date:format=yyyyMM}  
> - appsettings.json apllo配置更改 WMetaServer为"MetaServer": "http://172.17.0.5:6080/" 
> - appsettings.json apllo配置更改 MetaServer为"MetaServer": "http://172.17.0.5:6080"
- 4、将要发布的打包成**publish.zip**，注意 一定是要zip，放在latest文件夹下的你要发布的项目目录里
- 5、执行dycontrol help 命令，查看命令规则
- 6、通常发布 是 dycontrol release jdzng JDBoxApi DiYi.JDBox.WebApi.dll 12301
- 7、查看发布的进程id是否正常和输入日志是否正常
