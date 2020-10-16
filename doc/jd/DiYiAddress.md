## DiYiAddress

## Nuget 服务器
服务器：\\192.168.1.115\Packages 
账号：pingtai 
密码：Ptai1031!

### Jenkins灰度
- 登录账号：dyliyz
- 初始密码：L#2020!

### Jenkins测试
- 账户：pingtai
- 密码：IT06131118+

### ES地址：5601
- 账号：elastic
- 密码：Diyibox@2018

### Apollo 正式
- 用户 admin
- 密码 80Apollo!Adm
  
### Apollo 测试
- 用户 admin
- 密码 Apollo@dev1101

### 正式发布命令
- 小蜜蜂：dycontrol release xmf JDBeeAppApi DiYi.JDBeeApp.Api.dll 12303
- 京东快递员：dycontrol release jdkdy JDCourierApi JDCourierApi.dll 12302
- 京东云柜：dycontrol release jdzng JDBoxApi DiYi.JDBox.WebApi.dll 12301
- 京东入库：dycontrol release jdggyy expressInTask JDSendOrder.ExpressIn.dll 2
- 京东出库：dycontrol release jdggyy expressOutTask JDSendOrder.ExpressOut.dll 2
- 递驿APP：dycontrol release dapp courierStationAppApi CourierStationAppApi.dll 12304

### 正式重启命令
- 小蜜蜂：dycontrol restart xmf JDBeeAppApi DiYi.JDBeeApp.Api.dll 12303
- 京东快递员：dycontrol release jdkdy JDCourierApi JDCourierApi.dll 12302
- 京东云柜：dycontrol release jdzng JDBoxApi DiYi.JDBox.WebApi.dll 12301
- 京东入库：dycontrol restart jdggyy expressInTask JDSendOrder.ExpressIn.dll 2
- 京东出库：dycontrol restart jdggyy expressOutTask JDSendOrder.ExpressOut.dll 2

### 灰度命令
- 小蜜蜂： nohup dotnet DiYi.JDBeeApp.Api.dll urls=http://0.0.0.0:12251



