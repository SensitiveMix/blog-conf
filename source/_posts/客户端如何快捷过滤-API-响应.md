---
title: 客户端如何快捷过滤 API 响应
date: 2018-11-09 15:10:57
categories:
  - 开发
tags:
  - Frontend
---

## 前言
前后端分离服务模式下，联调成本以及沟通成本变高，如何提升客户端调用服务体验成主要因素。

* 需求变更频繁，API 无法每次都根据需求更新至最新
* 适应多端，各端需要的响应返回都不一样，不可能为每个端定制 API 响应
* 优化 API 响应时间，避免不必要的 Format 查询操作 
* 解决复杂冗余的 API 响应

业务场景中获取客户列表：
```
{
   "extra":{
      "orgCounts":1,
      "showHidden":true
   },
   "organizations":[
      {
         "_id":"5be51350f93a32ade0e2e257",
         "updateTime":"2018-11-09T04:55:45.203Z",
         "name":"蜀国",
         "_workspaceId":"5bd87ecba00f2b0001921b27",
         "fields":[


         ],
         "undoneActivities":0,
         "doneActivities":0,
         "totalActivities":0,
         "nextActivityDate":null,
         "lastActivityDate":null,
         "isPublic":false,
         "isArchive":false,
         "lostDeals":0,
         "wonDeals":0,
         "closedDeals":0,
         "openDeals":0,
         "numberOfEmployees":"",
         "quality":null,
         "status":{
            "_fixedFieldId":"5bd87ed761c2bf77fde85a2c",
            "value":"5bd87ed7d2e7854a5278b24a",
            "choices":[
               {
                  "pos":65536,
                  "value":"试用",
                  "_id":"5bd87ed7d2e7854a5278b24a"
               },
               {
                  "pos":131072,
                  "value":"Onboarding",
                  "_id":"5bd87ed7d2e7854a5278b249"
               },
               {
                  "pos":196608,
                  "value":"Retention",
                  "_id":"5bd87ed7d2e7854a5278b248"
               },
               {
                  "pos":262144,
                  "value":"Renew",
                  "_id":"5bd87ed7d2e7854a5278b247"
               },
               {
                  "pos":327680,
                  "value":"流失",
                  "_id":"5bd87ed7d2e7854a5278b246"
               }
            ]
         },
         "area":"",
         "city":"",
         "avatarUrl":"http://tcs.project.ci/thumbnail/010x5741bc1bce56f84c34f4b9855ce079b3/w/200/h/200",
         "province":"",
         "industry":"",
         "country":"",
         "address":"",
         "allocateTime":"2018-11-09T04:55:44.674Z",
         "updateNoteTime":null,
         "createdTime":"2018-11-09T04:55:44.674Z",
         "people":0,
         "note":"",
         "products":[


         ],
         "creator":{
            "_id":"599e33ce7c814524278308c2",
            "name":"jack"
         },
         "owner":{
            "_id":"599e33ce7c814524278308c2",
            "name":"jack"
         },
         "__v":0
      }
   ]
}
```

## 如何自定义 API 响应
API 调用函数可通过字段掩码的方式列出请求应返回或更新的字段。 使用字段掩码可使 API 避免执行不必要的工作，从而提升性能。 在 Slides API 中，读取和更新方法都会用到 [字段掩码](https://developers.google.com/slides/how-tos/field-masks)。

### 使用字段掩码进行读取

`Request`
```
https://www.googleapis.com/demo/v1?key=YOUR-API-KEY&fields=kind,items(title,characteristics/length)
```

`Response`
```
{
  "kind": "demo",
  "items": [{
    "title": "First title",
    "characteristics": {
      "length": "short"
    }
  }, {
    "title": "Second title",
    "characteristics": {
      "length": "long"
    }
  },
  ...
  ]
}
```


### 使用字段掩码进行更新
有时候，您只需要更新对象中的特定字段，而其他字段保持不变。 更新操作中的请求可使用字段掩码通知 API 正在更改哪些字段。此更新请求会忽略未在字段掩码中指定的任何字段，这些字段将保留各自的当前值。

您还可以取消设置字段，取消方法是，在更新的消息中不指定该字段，但将它添加到掩码。 这将清除该字段以前具有的任何值。

更新字段掩码的语法与读取字段掩码的语法相同。 此外，可将 * 字段掩码视作通配符，是一种简写形式，用于表示消息中的所有字段。


以下示例使用 `updateShapeProperties` 请求将形状的颜色填充更改为 DARK1 主题背景色，并取消设置形状的轮廓：
`Request`
```
PUT https://slides.googleapis.com/v1/presentations/presentationId:batchUpdate
```
```
{
  "requests": [
    {
      "updateShapeProperties": {
        "objectId": elementId,
        "shapeProperties": {
          "shapeBackgroundFill": {
            "solidFill": {
              "color": {
                "themeColor": "DARK1"
              }
            }
          }
        },
        "fields": "shapeBackgroundFill.solidFill.color,outline"
      }
    }
  ]
}
```

## Field Mask 字段参数语法摘要
fields 请求参数值的格式基于 [XPath](https://zh.wikipedia.org/wiki/XPath) 语法。支持的语法总结如下，以下部分提供了其他示例。

* 使用逗号分隔列表选择多个字段。
* 使用`a/b`选择字段`b`嵌套在字段内`a`; 用于`a/b/c`选择`c`嵌套在其中的字段`b`。
例外：对于使用“数据”包装器的API响应，其中响应嵌套在data看起来像的对象中`data: { ... }`，请不要`data`在`fields`规范中包含“ ” 。包含具有字段规范的数据对象`data/a/b`会导致错误。相反，只需使用`fields`类似的规范`a/b`。

* 使用子选择器通过将表达式放在括号“ ( )”中来请求一组数组或对象的特定子字段。
例如：`fields=items(id,author/email)` 仅返回 `items` 数组中每个元素的项ID和作者的电子邮件。您还可以指定单个子字段，其中 `fields=items(id)` 等效于 `fields=items/id`。

* 如果需要，请在字段选择中使用通配符。
例如：`fields=items/pagemap/*` 选择页面映射中的所有对象。

### 示例
| 例子 | 影响 |
| :------| ------: |
| items | 返回items数组中的所有元素，包括每个元素中的所有字段，但不包含其他字段 |
| etag,items | 返回etagitems数组中的字段和所有元素 |
| items/title   | 仅返回数组中 title 字段。每当返回嵌套字段时，响应都包含封闭的父对象。除非明确选择父字段，否则父字段不包括任何其他子字段。 | 
| context/facets/label   | 仅返回数组的label所有成员的字段，该facets数组本身嵌套在context对象下。   | 
| items/pagemap/*/title   | 对于items数组中的每个元素，仅返回作为子项title的所有对象的字段（如果存在）pagemap。   | 

## 应用实战
* 基础轮子：[JSON-MASK](https://github.com/nemtsov/json-mask)
* KOA2.X:  [koa-json-mask](https://github.com/nemtsov/koa-json-mask)
* KOA1.X:  [koa1-json-mask](https://github.com/sensitivemix/koa1-json-mask)

