# 通过SSH客户端连接堡垒机linux系统下发布应用
> 作者：李允智 
> 时间：2020-10-30
> 版本：1.1

## 前言
本文是基于晨阳的堡垒机应用JumpServer演示如何发布dotnet core应用，基于ssh客户端连接

必要条件
- 晨阳已分配了堡垒机的账号权限
- 下载"MobaXterm" SSH客户端连接工具

## 1、通过MobaXterm连接SSH客户端
### ![企业微信截图_15989540256035](https://raw.githubusercontent.com/liyunzhi1993/Image/master/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15989540256035.png)
> 按照图片所示
- 1、点击Session
- 2、点击SSH
- 3、Remote Host 输入：dyac.diyibox.net
- 4、Specify username 输入晨阳给你分配的账号 如我的 为liyz
- 5、Port 输入 2222
- 6、点击OK
- 7、在左侧列表 点击你添加的SSH连接 进入linux系统

## 2、进入linux系统

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
