# 发布计划

## 前言
> 发布计划是针对上预发布环境时需要准备的一些变更，以保证上线顺利，防止出现上线造成各种流程上异常的问题。

## 阿波罗配置变更
- Key:Value  说明

## 数据库字段变更
> - Sql语句1
> - Sql语句2
> - Sql语句3
> - Sql语句4

## 需要合并分支
>- gitlab项目名称：A分支到dev  dev-test test-master 
>- gitlab项目名称：A分支到dev  dev-test test-master 
>- gitlab项目名称：A分支到dev  dev-test test-master 

## Jenkins新增应用【按晨阳方式提供】
- IoT MQTT接口：
> 1、http://192.168.1.184/iot/DiYi.IoT
2、test
3、http://192.168.1.184/iot/DiYi.IoT/blob/dev/DiYi.IoT.MQTT/DiYi.IoT.MQTT.csproj
4、DiYi.IoT.MQTT.dll
5、递易IoT物联网平台MQTT开放接口【其他程序调用MQTT发送和接收消息的 对内】
6、无
7、.net core 3.1   
8、http://iotmqtt.diyibox.cn/
9、阿波罗 APPID：DiYiIotPlatform
10、Jenkins应用名：dev_iotapi_DiYilotMQTT

- IoT API接口：
>1、http://192.168.1.184/iot/DiYi.IoT
2、test
3、http://192.168.1.184/iot/DiYi.IoT/blob/dev/DiYi.IoT.API/DiYi.IoT.API.csproj
4、DiYi.IoT.API.dll
5、递易IoT物联网平台Server端【对内API，其他程序调用开箱等操作】
6、无
7、.net core 3.1    
8、http://iotapi.diyibox.cn/
9、阿波罗 APPID：DiYiIotPlatform
10、Jenkins应用名：dev_iotapi_DiYiIotAPI

## 发布项目【如果有顺序请说明】
> - jenkins应用名：xxxxxxx
> - jenkins应用名：xxxxxxx
> - jenkins应用名：xxxxxxx