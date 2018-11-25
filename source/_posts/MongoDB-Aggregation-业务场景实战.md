---
title: MongoDB Aggregation 业务场景实战
date: 2018-08-13 15:12:35
categories:
  - 数据库
tags:
  - MongoDB
  - Database
---

![图片](https://images-cdn.shimo.im/PYv34sYTcsMSc6Nb/image.png!thumbnail)

对于技术人员来说，“管道” 相信大家都不会感到陌生，在很多技术领域都有管道的概念，例如Linux 管道，CI/CD 管道。同样的，MongoDB 2.2 版本也新增了聚合管道功能，虽然功能发布已久，但是社区的复杂场景的实践并不多，给大家造成了**聚合管道**“不好用”的错觉 。实际在业务场景中，适当的运用聚合往往会带来事半功倍的效果。

# 定义
要想了解聚合管道在业务场景中的使用，首先需要了解**聚合管道**的定义：

聚合管道用于数据处理，每个文档通过一个或者是多个阶段组成，可以对每个分组进行**分组**和**过滤**等功能，然后经过一系列处理，输出相应的结果。

![图片](https://images-cdn.shimo.im/8BmyIAhe5HAeDqY5/image.png!thumbnail)

通过这张图，可以清晰的了解到**聚合管道**的处理过程，我们常用的管道操作符一般有以下这些：
* $match 主要用于对文档集合的筛选
* $project 主要用于从子文档中提取字段，可以重命名字段，也可以移除字段
* $group 主要用于根据文档的特定字段进行分组
* $unwind 主要用于分割数组嵌入到自己的顶层文件
* $lookup 主要用于两个集合之间的左连接操作
* $skip 接受一个数字 n，丢弃结果集中的前 n 个文档
* $limit 接受一个数字 n，返回结果集中的前 n 个文档
* $sort 主要用于结果集的排序
# 应用
看完了各种各样的管道操作符，或许有的人在想怎么把它利用在实际业务场景中呢？下面我就通过客户管理系统介绍一下聚合管道的最佳实践，大家听到客户管理系统可能有些陌生，它还有一个“别名” CRM。CRM 系统中存在机会、客户、联系人这三个大的对象，对象之间都是存在关联关系的，机会可以关联多个联系人，可以关联一个客户，而联系人和客户是一一对应的。通过三个基本对象我们可以衍生出很多聚合业务场景，基础架构图如下所示：
![图片](https://images-cdn.shimo.im/cRSY8YJpYh8vgzEs/crm架构图_1_.png!thumbnail)
图一：基础架构图


如上图所示，我们业务场景中经常会出现筛选数据的需求，如条件筛选和自定义字段查询等需求，根据特定的条件筛选出我们想要的数据。针对不同的业务需求，我们一般会涉及到以下场景：
* 基础对象查询
* 表 join 查询
* 分类统计
* 嵌套对象排序
* ...

说到了常见的应用场景，下面也介绍一下我们的系统业务数据模型：
* 机会数据模型
```
db.deals
{
  '_id': ObjectId,
  'title': String,               // 机会标题
  'status': String,              // 机会状态
  'peoples': Array,              // 关联联系人
  '_organizationId': ObjectId，  // 关联客户
  'createdTime': Date,           // 创建时间
  'updatedTime': Date,           // 创建时间
  'owner': Object                // 拥有者
}
```

* 联系人数据模型
```
db.peoples
{
  '_id': ObjectId,
  'name': String,                // 联系人名称
  'phone': Array,                // 电话
  'email': Array,                // 邮箱
  'isArchive': Boolean，         // 归档状态
  'createdTime': Date,           // 创建时间
  'updatedTime': Date,           // 创建时间
  'owner': Object                // 拥有者
}
```

* 客户数据模型
```
db.organizations
{
  '_id': ObjectId,
  'name': String,                // 客户名称
  'address': Array,              // 地址
  'isArchive': Boolean，         // 归档状态
  'createdTime': Date,           // 创建时间
  'updatedTime': Date,           // 创建时间
  'owner': Object                // 拥有者
}
```

有了数据模型，我们就可以用它来做数据聚合了，下面会列举出客户管理系统中常用的数据聚合实践。

1. 在 CRM 系统中，作为销售管理，他管辖的地区在指定时间生成的机会是他所关注的。如果他需要筛选出2018年5月1日之后创建的机会的跟进信息，可以通过创建时间的筛选实现他的目标，聚合管道语句如下：

涉及到的组合：**$match -> $project**

```
db.deals.aggregate([
  { $match: { createdTime: { '$gte': ISODate('2018-05-01') } } },
  { $project: { title: 1, owner: 1, status: 1, updatedTime: 1 } }
])
```

1. 在 CRM 系统中，作为销售管理，经常需要统计每个销售业绩情况，用于他们的业绩考核指标。所以他需要统计每个销售赢单的机会数目，为了达成这一目标他可以先通过匹配条件筛选出所有赢单的机会，并过滤所有拥有者为空，然后再通过人员分组，计算每个销售赢单的机会数，聚合管道语句如下所示：

涉及到的组合：**$match -> $group**

```
db.deals.aggregate([
  { $match: { status: 'won', owner: { '$ne': null } } },
  { $group: { _id: '$owner._id'，num_deals: { $sum: 1 } } }
])
```

1. 在 CRM 系统中，作为一名销售，经常需要拜访客户，拜访完成之后需要在机会中填写相应的跟进记录，所以对于他们来说需要完整的客户地址信息，用于日后的拜访使用。这时候他就可以根据拥有者是自己，并且关联的客户地址信息为空的筛选条件来找到那些机会信息缺失。一般情况下，便于记忆我们按照创建时间倒序要显示机会信息，聚合管道语句如下所示：

涉及到的组合：**$match -> $lookup -> $match -> $sort**

```
db.deals.aggregate([
  { $match: { 'owner.name': '张三' } },
  { $lookup: {
      from: 'organizations',
      localField: '_organizationId',
      foreignField: '_id',
      as: 'organization'
  } },
  { $match: { 'organization.address': {'$ne': ''} } },
  { $sort: { 'createdTime': -1 } }
])
```

1. 在 CRM 系统中，作为一名刚入职的销售，可以通过查看别人的赢单的跟单机会来学习如何跟单。这时候他就可以根据状态是赢单且拥有者是张三的筛选条件，找出别人最近赢单的50条销售机会来进行学习。聚合管道语句如下所示：

涉及到的组合：**$match -> $project -> $sort -> $skip -> $limit**

```
db.deals.aggregate([
  { $match: { 'status': 'won', 'owner.name': '张三' } },
  { $project: { 'name': 1, 'status': 1, 'wonTime': 1, 'note': 1 },
  { $sort: { 'wonTime': -1 },
  { $skip: 0 },
  { $limit: 50 }
])
```

1. 在 CRM 系统中，我们习惯把数据的完整性和成单概率挂钩，如果销售机会关联的联系人和客户信息缺失，往往代表这是一条成单率较低的机会。因为缺失联系人信息无法及时与客户联系会造成失单，所以我们可以通过聚合管道关联操作，寻找存在一个已归档或者拥有者为空的联系人和客户，找出对应的销售机会，评估该机会的信息缺失率，然后完善关联信息。聚合管道语句如下：

涉及到的组合：**$match -> $lookup -> $lookup -> $match -> $sort -> $skip -> $limit**

```
db.deals.aggregate([
  { $match: { 
      'owner.name': '张三',
      '$or': [
        { 'peoples': { '$ne': null } }, 
        { '_organizationId':{ '$ne': null } }
      ]
    }
  },
  { $lookup: {
      from: 'peoples',
      localField: 'peoples',
      foreignField: '_id',
      as: 'peoples'
  } },
  { $lookup: {
      from: 'organizations',
      localField: '_organizationId',
      foreignField: '_id',
      as: 'organization'
  } },
  { $match: { 
      '$or': [
        { 'peoples.isArchive': true },
        { 'peoples.owner': { '$ne': null } },
        { 'organization.isArchive': true },
        { 'organization.owner': { '$ne': null } }
      ]      
  },
  { $sort: { 'createdTime': -1 } },
  { $skip: 0 },
  { $limit: 50 }
])
```

# 技巧
我们在使用聚合管道满足我们的业务场景的同时，发现有很多小的技巧能够帮助我们优化数据查询，下面给大家列举一下：
* 管道操作符之**$ifNull**

定义：如果表达式计算为非空值，则计算表达式并返回表达式的值。如果表达式计算为空值，包括未定义的值或缺少字段的实例，则返回替换表达式的值。

如果需求是按照更新时间对未归档机会进行排序，普通的做法是：
```
db.deals.aggregate([
  { $match: { 'isArchive': false } },
  { $sort: { 'updatedTime': -1 }
])
```

这样存在一个问题，由于存在更新时间字段不存在或者值为空的脏数据，导致排序结果不准确，为了解决这个问题，当然我们也可以这样去做，排序里面指定多个字段排序：
```
db.deals.aggregate([
  { $match: { 'isArchive': false } },
  { $sort: { 'updatedTime': -1，'_id': -1 }
])
```

如果我们运用管道操作符 $ifNull 去实现的话，可以更改更新时间结构，并填充默认值来达到我们期望的排序结果：
```
db.deals.aggregate([
  { $match: { 'isArchive': false } },
  { $project: {
    title: 1,
    updatedTime: { $ifNull: [ "$updatedTime", ISODate('9000-01-01')] }
  } }
  { $sort: { 'updatedTime': -1}
])
```
使用 $ifNull 数据填充来进行排序效率比空值比较排序效率要高，MongoDB 官方也给出了排序类型效率顺序图，如下所示：

![图片](https://images-cdn.shimo.im/jYpiTSHI4u0QHuuY/image.png!thumbnail)
图二：不同类型排值图


* 管道操作符之$cond

定义：评估布尔表达式以返回两个指定的返回表达式之一。
如果我们要实现按照更新时间对未归档机会进行排序，更新时间为空的填充默认值，我们可以这样实现：
```
db.deals.aggregate([
  { $match: { 'isArchive': false } },
  { $project: {
    title: 1,
    updatedTime: { $cond: {
      if：{'$eq': ['$updatedTime', null]} ,
      then: ISODate('9000-01-01')
      else '$updatedTime'
    } }
  } }
  { $sort: { 'updatedTime': -1}
])

```


# 优化
* **策略优化**
  * 将** $match** 和 **$sort **放到管道的前面，可以给集合建立索引，来提高处理数据的效率
  * 可以用 **$match、$limit、$skip** 对文档进行提前过滤，以减少后续处理文档的数量
* **MongoDB 自身优化器**
  * **$sort **+ **$match** 顺序优化
    * 如果 $match 出现在 $sort 之后，优化器会自动把 $match 放到 $sort 前面
  * **$skip** + **$limit** 顺序优化
    * 如果 $skip 在 $limit 之后，优化器会把 $limit 移动到 $skip 的前面，移动后 $limit的值等于原来的值加上 $skip 的值
# 注意事项
* **返回结果大小**
  * 聚合结果返回的是一个文档，不能超过 16M，从 MongoDB 2.6版本以后，返回的结果可以是一个游标或者存储到集合中，返回的结果不受 16M 的限制。
* **内存**
  * 在进行 Group 操作的时候，如果内容超过 100 M，将会抛错  “**Exceeded memory limit for $group, but not allow enternal sort, put allowDiskUse true**”，如果需要处理大数据，可以使用 allowDiskUse 选项，存储到磁盘上**。**
* **聚合操作符使用**
  * 在进行 $project 操作的时候，我们时常会把不需要的值过滤掉，以此来减少聚合操作对内存的消耗，但是不可以删除默认的 _id, 如果删除会抛错 “**exception: The top-level _id field is the only field currently supported for exclusion**”，**_id** 作为官方字段不可以删除掉。

# 小结
今天我为你介绍了 MongoDB 聚合管道的应用实践、技巧、优化以及注意事项，解释了聚合管道操作符的基础概念，希望能够对你有所帮助。

这就是以上全部的内容，留几道思考题给你吧。你们公司使用 MongoDB 聚合管道吗？一般使用在什么业务上面？你觉得好用吗？
