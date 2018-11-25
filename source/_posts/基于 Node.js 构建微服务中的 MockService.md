---
title: 基于 Node.js 构建微服务中的 MockService
date: 2017-06-11 22:23:55
categories:
  - 开发
tags: 
  - Node.js
  - MockService
---

## 缘起
由于现在微服务越来越火了，越来越多的微服务融入到了日常开发当中。在开发微服务的时候，经常会遇到一个问题由于依赖于其他服务，导致你的进度受到阻碍。使你不得不先mock出你期望调用依赖服务的输出，来继续迭代开发。大部分情况下我们写在我们自己的服务中，因为这样方便省事，但是随着需要mock的服务越来越多，会发现越来越难管理，开发重构成本变高，于是在此基础上封装了一个基于Node.js可以根据配置文件生成Mock服务。

## 缘生
为了方便添加和删除不同的mock服务，将所有的mock服务写入了配置文件，你可以根据你所定义的配置文件生成mock服务，如下所示：

```bash
{
    'PATH':'./src/mocks',
    'PORT':'9001',
    "SERVICES":[
    {
      "name": "authorization",
      "Handlers": [
        {
          "method": "GET",
          "resHeader": "HTTP/1.1 200 OK",
          "resContent": "{ succeed: true,msg: ok }"
        },
        {
          "method": "POST",
          "resHeader": "HTTP/1.1 200 OK",
          "resContent": "{ succeed: true,msg: ok }"
        },
        {
          "method": "GET--a=b.mock",
          "resHeader": "HTTP/1.1 200 OK",
          "resContent": "{ succeed: true,msg: ok }"
        }
      ]
    }
    ]
}
```

Options:
  - `method`  Request Method
  - `GET--a=b.mock` Request GET /hello?a=b
  - `resHeader`  Response Header
  - `resContent` Response Content
  - `npm run mock`  Generate Mock Server
  - `npm run clean` Clean Mock Server



配置文件中的PATH是你存放的所有mock服务文件夹，填入请求的方式，以及期望的访问的HTTP状态和返回值。启动mock服务器，一个简单的mock服务就生成了。修改文件配置以后，可以清理生成的mock服务重新生成。
* 项目结构
<img src="http://images2015.cnblogs.com/blog/792678/201701/792678-20170111235643666-1668455875.png" width = "300" height = "470" alt="项目结构" align=center />
* 启动截图
<img src="http://images2015.cnblogs.com/blog/792678/201701/792678-20170111234932603-1644898172.png" width = "450" height = "200" alt="启动截图" align=center />
* 生成成功
<img src="http://images2015.cnblogs.com/blog/792678/201701/792678-20170111235914588-1492484987.png" width = "450" height = "200" alt="启动截图" align=center />
* 清理截图
<img src="http://images2015.cnblogs.com/blog/792678/201701/792678-20170111235157072-1187254879.png" width = "450" height = "200" alt="清理截图" align=center />

## 项目地址
* [node-mock-factory](https://github.com/sensitiveMix/node-mock-factory)
