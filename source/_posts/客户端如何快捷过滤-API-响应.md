---
title: 客户端如何快捷过滤 API 响应
date: 2018-11-09 15:10:57
categories:
  - 开发
tags:
  - Frontend
---

## 为什么要过滤
* 需求变更频繁，API 无法每次都根据需求更新
* 适应多端，各端需要的响应返回都不一样，不可能为每个端定制 API 返回
* 优化 API 响应时间，避免不必要的 Format 查询操作 
* 复杂冗余的 API 响应

获取客户列表：
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
[Field-Masks (字段掩码)](https://developers.google.com/slides/how-tos/field-masks)
* 使用字段掩码进行读取
| 例子   | 影响   | 
|:----|
| items   | 返回items数组中的所有元素，包括每个元素中的所有字段，但不包含其他字段。 | 
| etag,items   | 返回etagitems数组中的字段和所有元素。   | 
| items/title   | 仅返回titleitems数组中所有元素的字段。每当返回嵌套字段时，响应都包含封闭的父对象。除非明确选择父字段，否则父字段不包括任何其他子字段。 | 
| context/facets/label   | 仅返回数组的label所有成员的字段，该facets数组本身嵌套在context对象下。   | 
| items/pagemap/*/title   | 对于items数组中的每个元素，仅返回作为子项title的所有对象的字段（如果存在）pagemap。   | 


* 使用字段掩码进行更新
```
POST https://slides.googleapis.com/v1/presentations/presentationId:batchUpdate
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

## 如何应用中至项目
* 基础轮子：[JSON-MASK](https://github.com/nemtsov/json-mask)
* KOA2.X:  [koa-json-mask](https://github.com/nemtsov/koa-json-mask)
* KOA1.X:  [koa1-json-mask](https://github.com/sensitivemix/koa1-json-mask)

