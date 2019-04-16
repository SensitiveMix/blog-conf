---
title: '如何书写 Node DEV API 文档 '
date: 2019-04-16 17:02:20
tags: 
  - Node.js
  - Swagger Doc
---

## 为什么需要 API 文档
目前大的环境下面，基本服务都以单体服务转换成前后端分离的服务，客户端作为调用方，服务端作为提供方需要给予到调用方通俗易懂的文档。
## 常见 API 文档有哪些
1. [gitbook](https://github.com/GitbookIO/gitbook)
> Modern documentation format and toolchain using Git and Markdown 

![图片](https://uploader.shimo.im/f/45J30B4D0d4L7CWC.png!thumbnail)
* [Usage](https://github.com/GitbookIO/gitbook/blob/master/docs/setup.md)
* 优点：
  * 类似书籍的管理方式（适合写书）
  * 支持多人协作，支持保存在 Github 中
  * 支持 Build 成静态网页，部署方便
* 缺点：
  * 需要熟悉 **Markdown **语法，不是实时生成，需要使用 [Git-cli](https://github.com/GitbookIO/gitbook-cli) 打包文件
  * 以一本书籍作为起点，只能放到单个项目中
* 总结：适合写书籍，不太适合 API 开发文档

2. [jsdoc](https://github.com/jsdoc3/jsdoc)
>An API documentation generator for JavaScript.

![图片](https://uploader.shimo.im/f/UyoiKuM54H08ylGa.png!thumbnail)
* [Usage](http://usejsdoc.org/)
* 优点：
  * 功能丰富，支持引入块状注释（[Block Tags](https://www.html.cn/doc/jsdoc/tags-example.html)）
  * 功能丰富，支持内联注释（[Inline Tags](https://www.html.cn/doc/jsdoc/tags-inline-link.html)）
  * 强大的社区支持，各个平台支持 jsdocs 插件
* 缺点：
  * 有一定的学习成本，需要学习它的块状注释使用方式
  * UI 只能单项目使用，每个项目的 DOC UI 独立 
* 在线样例（[b](https://github.com/SoftwareBrothers/better-docs)[etter-](https://softwarebrothers.github.io/admin-bro-dev/index.html)[d](https://github.com/SoftwareBrothers/better-docs)[ocs](https://github.com/SoftwareBrothers/better-docs) [tui-jsdoc-template](https://nhnent.github.io/tui.jsdoc-template/latest/))
* 总结：适合深度定制项目，比如说我们对外开放 API 给开发者使用。

3. [swagger-jsdoc](https://github.com/Surnet/swagger-jsdoc)
>Generates swagger doc based on JSDoc.

![图片](https://uploader.shimo.im/f/fHtWq6Xh6gcnZNZm.png!thumbnail)
* [Usage](https://github.com/Surnet/swagger-jsdoc/tree/master/example/v2)
* 优点：
  * 集合 Swagger，支持按照类似 JSDoc 方式生成文档
  * 支持错误定位，如果注释书写错误，能够抛出具体的错误栈
  * 支持 [CLI](https://github.com/Surnet/swagger-jsdoc/blob/master/docs/CLI.md)，自由选择文件输出位置
* 缺点：
  * 书写格式不友好，强格式要求，对于新手来说不容易上手，官方样例较少，很多语法需要对于 Swagger YAML 格式，自己理解进行映射
  * 没有能够完成安装 JSDoc 格式来书写注释，更倾向于 YAML 文件

4. [doc-generators](https://github.com/SensitiveMix/doc-generators)
>simple generator handler for swagger
```
/**
 * @typedef Product
 * @property {integer} id
 * @property {string} name.required - Some description for product
 * @property {Array.<Point>} Point
 */

/**
 * This function comment is parsed by doctrine
 * sdfkjsldfkj
 * @route POST /users
 * @param {Point.model} point.body.required - the new point
 * @group foo - Operations about user
 * @param {string} email.query.required - username or email
 * @param {string} password.query.required - user's password.
 * @param {enum} status.query.required - Status values that need to be considered for filter - eg: available,pending
 * @operationId retrieveFooInfo
 * @produces application/json application/xml
 * @consumes application/json application/xml
 * @returns {Response.model} 200 - An array of user info
 * @returns {Product.model}  default - Unexpected error
 * @returns {Array.<Point>} Point - Some description for point
 * @headers {integer} 200.X-Rate-Limit - calls per hour allowed by the user
 * @headers {string} 200.X-Expires-After - 	date in UTC when token expires
 * @security JWT
 */
```

* [Usage](https://github.com/SensitiveMix/doc-generators/blob/master/example/router.js)
* 优点：
  * 支持集成 Swagger，支持按照类似 JSDoc 方式生成文档
  * 友好的文档的注释，贴合 IDE 函数注释
* 缺点：
  * 实现可用的基础上，需要支持到 IDE，方便一键生成，支持自动化
## Example
```
/**
 * verify quarantine site
 * @route POST /ui/quarantine/v1/verify_od_dedicated_quarantine_site
 * @group quarantine
 * @param {QuarantineJSON.model} body.body.required - post body
 * @returns {object} 200 - An array of handler info
 * @returns {Error} 400 - invalid site
 * @returns {Error} 500 - internal error
 */
/**
 * JSON parameters require a model. This one just has "name"
 * @typedef QuarantineJSON
 * @property {string} site.required - quarantine site info
 */
    server.post('/ui/quarantine/v1/verify_od_dedicated_quarantine_site', middleware.other.verifyLoginInfo,
        rbac.quarantine.write, quarantine.verifyOdDedicatedQuarantineSite);
```

```
/**
 * HTTP API statistics
 * @route GET /monit/v1/metric
 * @group monit
 * @param {string} sort.query - asc | desc
 * @param {string} direction.query - count | avarageMillisecondCost | routeName
 * @returns {object} 200 - An array of user info
 * @returns {metrics.model} default - Unexpected error
 */
/**
 * @typedef metrics
 * @property {string} routeName.required - 'getuiadminv1company_plans'
 * @property {number} count.required - route invoke times
 * @property {number} avarageMillisecondCost.required - route avg second cost
 */

server.get("/monit/v1/metric", validator.query({
    type: 'object',
    properties: {
        sort: { type: 'string', enum: ['count'] },
        direction: { type: 'string', enum: ['asc', 'desc'] }
    }
}), monitService.apiMetricHandlers);
```

项目地址：[https://github.com/SensitiveMix/doc-generators](https://github.com/SensitiveMix/doc-generators)
喜欢的同学请点个 Star 😆
