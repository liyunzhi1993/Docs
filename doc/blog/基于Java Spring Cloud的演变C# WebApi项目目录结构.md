# 基于Java Spring Boot的演变C#  WebApi项目目录结构

## 前言
> 做过C# WebApi的一定深有体会一点，不同的项目就不说编码风格的不同了，项目结构由于不同的解决方案创建也有很大区别。而java这块会好很多，大致的项目结构风格都差不多，本文是基于Java Spring Boot的演变C# .Net Core WebApi项目目录结构

> 代码必须要好看！

## 架构演变
- Spring Boot -> Asp Net Core Web Api
- Mybatis -> Dapper
- Rest -> Rest

## 原则
- Service层跟随WebApi应用，在微服务架构中也是如此
- Controller中不写业务代码
- 通过反射注入IOC
- Service层必须定义Interface
- 定义Dao层，对应Sql语句，同样注入IOC
- 定义Entity层，对应数据库表实体
- 利用AOP进行JwtToken验证

## 目录结构

![企业微信截图_15972254481799](https://raw.githubusercontent.com/liyunzhi1993/Image/master/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_15972254481799.png)

MQTT.API                                                
├─ MQTT.API   API应用                                      
│  ├─ Controllers                                      
│  │  └─ ApiController.cs                               
│  ├─ Service   业务Interface                                
│  │  └─ IMQTTService.cs                                
│  ├─ ServiceImpl   业务Interface实现                                       
│  │  └─ MQTTServiceImpl.cs                                                                 
├─ MQTT.Common            公用类库                              
│  ├─ Enum                        枚举                      
│  │  ├─ DbConnectionNameEnum.cs                        
│  │  ├─ DeviceCommandTypeEnum.cs                       
│  │  ├─ EnumExtention.cs                               
│  │  ├─ IoTSubscribeMqttTopicEnum.cs                   
│  │  ├─ MqttReceiveStatusEnum.cs                       
│  │  ├─ RedisKeyEnum.cs                                
│  │  └─ ResponseCodeEnum.cs                            
│  ├─ Filter          AOP                                  
│  │  └─ ModelActionFilter.cs                           
│  ├─ Middleware              中间件                          
│  │  └─ CustomExceptionHandlerMiddleware.cs            
│  ├─ MQTT               MQTT                               
│  │  ├─ MqttConfig.cs                                  
│  │  └─ MqttNetClient.cs                               
│  ├─ Util             操作类                                 
│  │  ├─ EncryptUtil.cs                                 
│  │  └─ IoCUtil.cs                                                
├─ MQTT.Dao                数据库Dao层                             
│  ├─ Impl                    Dao层实现                          
│  │  ├─ DbConnectionFactoryImpl.cs                     
│  │  └─ DeviceConfigDaoImpl.cs                         
│  ├─ BaseDao.cs                                        
│  ├─ IDbConnectionFactory.cs        Dao层Interface                   
│  ├─ IDeviceConfigDao.cs                               
├─ MQTT.Entity                 数据库表对应实体层                         
│  ├─ DeviceConfig.cs                                        
├─ MQTT.Model            模型层                               
│  ├─ In                       入参模型                         
│  │  ├─ AuthorizeInModel.cs                            
│  │  ├─ GetConfigInModel.cs                            
│  │  ├─ PingInModel.cs                                 
│  │  ├─ PublishMessageInModel.cs                       
│  │  ├─ RebootInModel.cs                               
│  │  ├─ ReceiveMessageInModel.cs                       
│  │  ├─ SetConfigInModel.cs                            
│  │  ├─ SetDataInModel.cs                              
│  │  ├─ StatusInModel.cs                               
│  │  └─ UpdateInModel.cs                               
│  ├─ Out                                    出参模型           
│  │  ├─ AuthorizeOutModel.cs                           
│  │  ├─ BaseApiOutModel.cs                             
│  │  ├─ GetConfigOutModel.cs                           
│  │  ├─ GetDataOutModel.cs                             
│  │  ├─ PingOutModel.cs                                
│  │  ├─ PublishMessageOutModel.cs                      
│  │  └─ ReceiveMessageOutModel.cs                      
│  ├─ BaseIoTModel.cs        基础模型                           
│  ├─ BaseOutModel.cs                                   
│  ├─ MqttContentModel.cs                               
│  └─ MqttPayloadModel.cs                               
└─ README.md                                            

## 项目地址
[点我前往github](https://github.com/liyunzhi1993/MQTT.HTTP.Api)

