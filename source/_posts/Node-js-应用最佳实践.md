---
title: Node.js 应用最佳实践
date: 2018-09-03 15:01:29
categories:
  - 总结
tags: 
  - Node.js
  - BestPractice
---

>基于 [Express](https://github.com/expressjs/express) 实践，Express 是一个简洁而灵活的 node.js Web应用框架, 提供了一系列强大特性帮助你创建各种 Web 应用，和丰富的 HTTP 工具。

1. 项目结构实践
## 1.1 组件式构建你的解决方案 (The Future)
定义：大型项目的最坏的隐患就是维护一个庞大的，含有几百个依赖的代码库 - 当开发人员准备整合新的需求的时候，这样一个庞然大物势必减缓了开发效率。反之，把您的代码拆分成组件，每一个组件有它自己的文件夹和代码库，并且确保每一个组件小而简单。

否则: 当编写新需求的开发人员逐步意识到他所做改变的影响，并担心会破坏其他的依赖模块 - 部署会变得更慢，风险更大。当所有业务逻辑没有被分开，这也会被认为很难扩展
### 1.1.1 Bad: Group your files by technical role
![图片](https://github.com/i0natan/nodebestpractices/raw/master/assets/images/structurebyroles.PNG)
### 1.1.2 Good: Structure your solution by self-contained components
![图片](https://github.com/i0natan/nodebestpractices/raw/master/assets/images/structurebycomponents.PNG)
## 1.2 分层设计组件，保持项目结构在特定的区域
定义: 每一个组件都应该包含「层级」 - 一个专注的用于接入网络，逻辑，数据的概念。这样不仅获得一个清晰的分离考量，而且使仿真（mock-server）和测试系统变得异常容易。尽管这是一个普通的模式，但接口开发者易于混淆层级关系，比如把网络层的对象（req, res）传给业务逻辑和数据层 - 这会令您的应用彼此依赖，并且只能通过Express使用。

否则: 对于混淆了网络层和其它层的应用，将不易于测试，执行 CRON 的任务，其它非-Express 的调用者无法使用


![图片](https://github.com/i0natan/nodebestpractices/raw/master/assets/images/structurebycomponents.PNG)

>对比逻辑分层重要性

![图片](https://github.com/i0natan/nodebestpractices/blob/master/assets/images/keepexpressinweb.gif?raw=true)
## 1.3 封装公共模块成为NPM的包
定义: 由大量代码构成的一个大型应用中，贯彻全局的，比如日志，加密和其它类似的公共组件，应该进行封装，并暴露成一个私有的NPM包。这将使其在更多的代码库和项目中被使用变成了可能。

否则: 您将不得不重造部署和依赖的轮子
### 在环境和组件中共享你自己的公用实用工具

![图片](https://github.com/i0natan/nodebestpractices/raw/master/assets/images/Privatenpm.png)

## 1.4 分离 Express 'app' and 'server'
定义: 避免定义整个 [Express](https://expressjs.com/) 应用在一个单独的大文件里， 这是一个不好的习惯 - 分离您的 「Express」 定义至少在两个文件中： API声明(app.js) 和 网络相关(WWW)。对于更好的结构，是把你的API声明放在组件中。

否则: 您的 API 将只能通过 HTTP 的调用进行测试（慢，并且很难产生测试覆盖报告）。维护一个有着上百行代码的文件也不是一个令人开心的事情。

### 一段解释
最新的 Express 生成器有一个值得保留的伟大实践--API声明与网络相关配置（端口、协议等）是分开的。这样就可以在不执行网络调用的情况下对API进行在线测试，它所带来的好处是：快速执行测试操作和获取代码覆盖率。它还允许在灵活多样的网络条件下部署相同的API。额外好处：更好的关注点分离和更清晰的代码结构。

### 代码示例：API声明应该在 app.js 文件里面
```
var app = express();
app.use(bodyParser.json());
app.use("/api/events", events.API);
app.use("/api/forms", forms);
```
### 
### 代码示例: 服务器网络声明，应该在 /bin/www 文件里面
```
var app = require('../app');
var http = require('http');

/**
 * Get port from environment and store in Express.
 */

var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

/**
 * Create HTTP server.
 */

var server = http.createServer(app);
```
### 
### 示例代码: 使用超快的流行的测试包在线测试你的代码
```
const app = express();

app.get('/user', function(req, res) {
  res.status(200).json({ name: 'tobi' });
});

request(app)
  .get('/user')
  .expect('Content-Type', /json/)
  .expect('Content-Length', '15')
  .expect(200)
  .end(function(err, res) {
    if (err) throw err;
  });
```

## 1.5 使用易于设置环境变量，安全和分级的配置
定义: 一个完美无瑕的配置安装应该确保 (a) 元素可以从文件中，也可以从环境变量中读取 (b) 密码排除在提交的代码之外 (c) 为了易于检索，配置是分级的。仅有几个包可以满足这样的条件，比如[rc](https://www.npmjs.com/package/rc), [nconf](https://www.npmjs.com/package/nconf) 和 [config](https://www.npmjs.com/package/config)。

否则: 不能满足任意的配置要求将会使开发，运维团队，或者两者，易于陷入泥潭。

### 一段解释
当我们处理配置参数时，常常会很慢并且很烦躁：
（1）当需要注入100个keys(而不是只在配置文件中提交它们)时，使用进程环境变量设置所有的keys变得非常繁琐，但是当处理只有devops管理权限的文件时，不改变代码行为就不不会变。一个可靠的配置解决方案必须结合配置文件和进程变量覆盖。

（2）枚举一个普通JSON的所有keys时，当目录变得非常庞杂的时候，查找修改条目困难。几乎没有配置库允许将配置存储在多个文件中，运行时将所有文件联合起来。分成几个部分的分层JSON文件能够克服这个问题。请参照下面示例。

（3）不推荐存储像密码数据这样的敏感信息，但是又没有快速便捷的方法解决这个难题。一些配置库允许文件加密，其他库在Git提交时加密目录，或者不存储这些目录的真实值，在通过环境变量部署期间枚举真实值。

（4）一些高级配置场景需要通过命令行（vargs）注入配置值，或者像Redis一样通过集中缓存同步配置信息，所以不同的服务器不会保存不同的数据。
一些配置库可以免费提供这些功能的大部分功能，请查看NPM库（[nconf](https://www.npmjs.com/package/nconf) 和 [config](https://www.npmjs.com/package/config)）这些库可以满足这些要求中的许多要求。

### 代码示例 – 分层配置有助于查找条目和维护庞大的配置文件
```
{
  // Customer module configs 
  "Customer": {
    "dbConfig": {
      "host": "localhost",
      "port": 5984,
      "dbName": "customers"
    },
    "credit": {
      "initialLimit": 100,
      // Set low for development 
      "initialDays": 1
    }
  }
}
```


1. 异常处理实践
## 2.1 使用 Async-Await 和 promises 用于异步错误处理
定义: 使用回调的方式处理异步错误可能是导致灾难的最快的方式(a.k.a the pyramid of doom)。对您的代码来说，最好的礼物就是使用规范的 promise 库、 async-await 来替代，这会使其像 try-catch 一样更加简洁，具有熟悉的代码结构。

否则: Node.js回调特性, function(err, response), 是导致不可维护代码的一个必然的方式。究其原因，是由于混合了随意的错误处理代码，臃肿的内嵌，蹩脚的代码模式。

### 代码示例 – 使用promise捕获错误
```
doWork()
 .then(doWork)
 .then(doOtherWork)
 .then((result) => doWork)
 .catch((error) => {throw error;})
 .then(verify);
```
### 
### 代码示例 反模式 – 回调方式的错误处理
```
getData(someParameter, function(err, result){
    if(err != null)
      //做一些事情类似于调用给定的回调函数并传递错误
      getMoreData(a, function(err, result){
        if(err != null)
          //做一些事情类似于调用给定的回调函数并传递错误
          getMoreData(b, function(c){ 
            getMoreData(d, function(e){ 
              if(err != null)
                //你有什么想法? 
    });
});
```

## 2.2 仅使用内建的错误对象
定义: 很多人抛出异常使用字符串类型或一些自定义类型 - 这会导致错误处理逻辑和模块间的调用复杂化。是否您reject一个promise，抛出异常或发出(emit)错误 - 使用内建的错误对象将会增加设计一致性，并防止信息的丢失。

否则: 调用某些模块，将不确定哪种错误类型会返回 - 这将会使恰当的错误处理更加困难。更坏的情况是，使用特定的类型描述错误，会导致重要的错误信息缺失，比如 stack trace！

### 代码示例 – 正确处理它
```
//从典型函数抛出错误, 无论是同步还是异步
 if(!productToAdd)
 throw new Error("How can I add new product when no value provided?");
 
//从EventEmitter抛出错误
const myEmitter = new MyEmitter();
myEmitter.emit('error', new Error('whoops!'));
 
//从promise抛出错误
 return new promise(function (resolve, reject) {
	Return DAL.getProduct(productToAdd.id).then((existingProduct) => {
		 if(existingProduct != null)
			 reject(new Error("Why fooling us and trying to add an existing product?"));
```
### 
### 代码示例 – 反模式
```
//抛出字符串错误缺少任何stack trace信息和其他重要属性
if(!productToAdd)
    throw ("How can I add new product when no value provided?");
```
### 
### 代码示例 – 更好处理它
```
//从node错误派生的集中错误对象
function appError(name, httpCode, description, isOperational) {
    Error.call(this);
    Error.captureStackTrace(this);
    this.name = name;
    //...在这赋值其它属性
};

appError.prototype.__proto__ = Error.prototype;

module.exports.appError = appError;
 
//客户端抛出一个错误
if(user == null)
  throw new appError(commonErrors.resourceNotFound, commonHTTPErrors.notFound, "further explanation", true)
```


## 2.3 区分运行错误和程序设计错误
定义: 运行错误（例如, API接受到一个无效的输入）指的是一些已知场景下的错误，这类错误的影响已经完全被理解，并能被考虑周全的处理掉。同时，程序设计错误（例如，尝试读取未定义的变量）指的是未知的编码问题，影响到应用得当的重启。

否则: 当一个错误产生的时候，您总是得重启应用，但为什么要让 ~5000 个在线用户不能访问，仅仅是因为一个细微的，可以预测的，运行时错误？相反的方案，也不完美 – 当未知的问题（程序问题）产生的时候，使应用依旧可以访问，可能导致不可预测行为。区分两者会使处理更有技巧，并在给定的上下文下给出一个平衡的对策。

### 一段解释
区分以下两种错误类型将最大限度地减少应用程序停机时间并帮助避免出现荒唐的错误: 操作型错误指的是您了解发生了什么情况及其影响的情形 – 例如, 由于连接问题而导致对某些 HTTP 服务的查询失败问题。另一方面, 程序型错误指的是您不知道原因, 有时是错误不知道来自何处的情况 – 可能是一些代码试图读取未定义的值或 DB 连接池内存泄漏。操作型错误相对容易处理 – 通常记录错误就足够了。当程序型错误出现，事情变得难以应付, 应用程序可能处于不一致状态, 你可以做的，没有什么比优雅的重新启动更好了。

### 代码示例 – 将错误标记为可操作 (受信任)
```
//将错误标记为可操作 
var myError = new Error("How can I add new product when no value provided?");
myError.isOperational = true;
 
//或者, 如果您使用的是一些集中式错误工厂 (请参见项目符号中的示例"仅使用内置错误对象")
function appError(commonType, description, isOperational) {
    Error.call(this);
    Error.captureStackTrace(this);
    this.commonType = commonType;
    this.description = description;
    this.isOperational = isOperational;
};
 
throw new appError(errorManagement.commonErrors.InvalidInput, "Describe here what happened", true);
```
## 
## 2.4 集中处理错误，不要在 Express 中间件中处理错误
定义: 错误处理逻辑，比如给管理员发送邮件，日志应该封装在一个特定的，集中的对象当中，这样当错误产生的时候，所有的终端（例如 Express中间件，cron 任务，单元测试）都可以调用。

否则: 错误处理的逻辑不放在一起将会导致代码重复和非常可能不恰当的错误处理。

### 一段解释
如果没有一个专用的错误处理对象，那么由于操作不当，在雷达下重要错误被隐藏的可能性就会更大。错误处理对象负责使错误可见，例如通过写入一个格式化良好的logger，通过电子邮件将事件发送到某个监控产品或管理员。一个典型的错误处理流程可能是：一些模块抛出一个错误 -> API路由器捕获错误 -> 它传播错误给负责捕获错误的中间件（如Express，KOA）-> 集中式错误处理程序被调用 -> 中间件正在被告之这个错误是否是一个不可信的错误（不是操作型错误），这样可以优雅的重新启动应用程序。注意，在Express中间件中处理错误是一种常见但又错误的做法，这样做不会覆盖在非Web接口中抛出的错误。

### 代码示例 – 一个典型错误流
```
//DAL层, 在这里我们不处理错误
DB.addDocument(newCustomer, (error, result) => {
    if (error)
        throw new Error("Great error explanation comes here", other useful parameters)
});
 
//API路由代码, 我们同时捕获异步和同步错误，并转到中间件
try {
    customerService.addNew(req.body).then(function (result) {
        res.status(200).json(result);
    }).catch((error) => {
        next(error)
    });
}
catch (error) {
    next(error);
}
 
//错误处理中间件，我们委托集中式错误处理程序处理错误
app.use(function (err, req, res, next) {
    errorHandler.handleError(err).then((isOperationalError) => {
        if (!isOperationalError)
            next(err);
    });
});
```
### 
### 代码示例 – 在一个专门的对象里面处理错误
```
module.exports.handler = new errorHandler();
 
function errorHandler(){
    this.handleError = function (error) {
        return logger.logError(err).then(sendMailToAdminIfCritical).then(saveInOpsQueueIfCritical).then(determineIfOperationalError);
    }
```

## 2.5 对API错误使用Swagger文档化
定义: 让你的API调用者知道哪种错误会返回，这样他们就能完全的处理这些错误，而不至于系统崩溃。Swagger，REST API的文档框架，通常处理这类问题。

否则: 任何API的客户端可能决定崩溃并重启，仅仅因为它收到一个不能处理的错误。注意：API的调用者可能是你（在微服务环境中非常典型）。

## 2.6 当一个特殊的情况产生，停掉服务是得体的
定义: 当一个不确定错误产生（一个开发错误，最佳实践条款#3) - 这就意味着对应用运转健全的不确定。一个普通的实践将是建议仔细地重启进程，并使用一些‘启动器’工具，比如Forever和PM2。

否则: 当一个未知的异常被抛出，意味着某些对象包含错误的状态（例如某个全局事件发生器由于某些内在的错误，不在产生事件），未来的请求可能失败或者行为异常。

### 代码实例: 决定是否退出
```
//收到未捕获的异常时，决定是否要崩溃
//如果开发人员标记已知的操作型错误使用：error.isOperational=true

process.on('uncaughtException', function(error) {
 errorManagement.handler.handleError(error);
 if(!errorManagement.handler.isTrustedError(error))
 process.exit(1)
});
 
 
//封装错误处理相关逻辑在集中的错误处理中
function errorHandler(){
 this.handleError = function (error) {
 return logger.logError(err).then(sendMailToAdminIfCritical).then(saveInOpsQueueIfCritical).then(determineIfOperationalError);
 }
 
 this.isTrustedError = function(error)
 {
 return error.isOperational;
 }
```

## 2.7 使用一个成熟的日志工具提高错误的可见性
定义: 一系列成熟的日志工具，比如Winston，Bunyan和Log4J，会加速错误的发现和理解。忘记console.log吧。

否则: 浏览console的log，和不通过查询工具或者一个好的日志查看器，手动浏览繁琐的文本文件，会使你忙于工作到很晚。

### 代码示例 – 使用Winston Logger
```
//您的集中式logger对象
var logger = new winston.Logger({
  level: 'info',
  transports: [
    new (winston.transports.Console)(),
    new (winston.transports.File)({ filename: 'somefile.log' })
  ]
});

//在某个地方使用logger的自定义代码
logger.log('info', 'Test Log Message with some parameter %s', 'some parameter', { anything: 'This is metadata' });
```
### 
### 代码示例 – 查询日志文件夹 (搜索条目)
```
var options = {
    from: new Date - 24 * 60 * 60 * 1000,
    until: new Date,
    limit: 10,
    start: 0,
    order: 'desc',
    fields: ['message']
  };


  // 查找在今天和昨天之间记录的项目
  winston.query(options, function (err, results) {
    //对于结果的回调处理
  });
```


## 2.8 使用你最喜欢的测试框架测试错误流
定义: 无论专业的自动化测试或者简单的手动开发测试 - 确保您的代码不仅满足正常的场景，而且处理并且返回正确的错误。测试框架，比如Mocha & Chai可以非常容易的处理这些问题（在"Gist popup"中查看代码实例）。

否则: 没有测试，不管自动还是手动，您不可能依赖代码去返回正确的错误。而没有可以理解的错误，那将毫无错误处理可言。

### 代码示例: 使用 Mocha & Chai 确保正确的异常被抛出
```
describe("Facebook chat", () => {
 it("Notifies on new chat message", () => {
 var chatService = new chatService();
 chatService.participants = getDisconnectedParticipants();
 expect(chatService.sendMessage.bind({message: "Hi"})).to.throw(ConnectionError);
 });
});
```
### 
### 代码示例: 确保 API 返回正确的 HTTP 错误码
```
it("Creates new Facebook group", function (done) {
 var invalidGroupInfo = {};
 httpRequest({method: 'POST', uri: "facebook.com/api/groups", resolveWithFullResponse: true, body: invalidGroupInfo, json: true
 }).then((response) => {
 //oh no if we reached here than no exception was thrown
 }).catch(function (response) {
 expect(400).to.equal(response.statusCode);
 done();
 });
 });
```

## 2.9 捕获未处理的promise rejections
定义: 任何在promise中被抛出的异常将被收回和遗弃，除非开发者没有忘记去明确的处理。即使您的代码调用的是process.uncaughtException！解决这个问题可以注册到事件process.unhandledRejection。

否则: 您的错误将被回收，无踪迹可循。没有什么可以需要考虑。

### 代码示例: 这些错误将不会得到任何错误处理程序捕获（除unhandledrejection）
```
DAL.getUserById(1).then((johnSnow) =>
{
  //this error will just vanish
	if(johnSnow.isAlive == false)
	    throw new Error('ahhhh');
});
```
### 代码示例: 捕获 unresolved 和 rejected 的 promise
```
process.on('unhandledRejection', (reason, p) => {
  //我刚刚捕获了一个未处理的promise rejection, 因为我们已经有了对于未处理错误的后备的处理机制（见下面）, 直接抛出，让它来处理
  throw reason;
});
process.on('uncaughtException', (error) => {
  //我刚收到一个从未被处理的错误，现在处理它，并决定是否需要重启应用
  errorManagement.handler.handleError(error);
  if (!errorManagement.handler.isTrustedError(error))
    process.exit(1);
});
```

## 2.10 快速查错，验证参数使用一个专门的库Fail fast, validate arguments using a dedicated library

定义: 这应该是您的Express最佳实践中的一部分 – assert API输入避免难以理解的漏洞，这类漏洞以后会非常难以追踪。而验证代码通常是一件乏味的事情，除非使用一些非常炫酷的帮助库比如Joi。

否则: 考虑这种情况 – 您的功能期望一个数字参数 “Discount” ，然而调用者忘记传值，之后在您的代码中检查是否 Discount!=0 （允许的折扣值大于零），这样它将允许用户使用一个折扣。OMG，多么不爽的一个漏洞。你能明白吗？

### 代码示例: 使用‘Joi’验证复杂的JSON输入
```
var memberSchema = Joi.object().keys({
 password: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/),
 birthyear: Joi.number().integer().min(1900).max(2013),
 email: Joi.string().email()
});
 
function addNewMember(newMember)
{
 //assertions come first
 Joi.assert(newMember, memberSchema); //throws if validation fails
 //other logic here
}
```
### 
### 反模式: 没有验证会产生令人讨厌的错误
```
//假如折扣为正，重定向用户去打印他的折扣优惠劵
function redirectToPrintDiscount(httpResponse, member, discount)
{
    if(discount != 0)
        httpResponse.redirect(`/discountPrintView/${member.id}`);
}
 
redirectToPrintDiscount(httpResponse, someMember);
//忘记传递参数discount, 为什么用户被重定向到折扣页面？
```

2. 编码规范实践
## 3.1 使用一款风格管理工具
定义: [ESLint](https://eslint.org/)，[Standard](https://github.com/standard/standard) 是检查可能的代码错误和修复代码样式的事实上的标准，不仅可以识别实际的间距问题, 而且还可以检测严重的反模式代码, 如开发人员在不分类的情况下抛出错误。尽管ESlint可以自动修复代码样式，但其他的工具比如 [prettier](https://www.npmjs.com/package/prettier) 和 [beautify](https://www.npmjs.com/package/js-beautify)在格式化修复上功能强大，可以和Eslint结合起来使用。

否则: 开发人员将必须关注单调乏味的间距和线宽问题, 并且时间可能会浪费在过多考虑项目的代码样式。

## 3.2 变量、常量、函数和类的命名约定
定义: 当命名变量和方法的时候，使用 *lowerCamelCase* ，当命名类的时候，使用 *UpperCamelCase* （首字母大写），对于常量，则 *UPPERCASE* 。这将帮助您轻松地区分普通变量/函数和需要实例化的类。使用描述性名称，但使它们尽量简短。

否则: JavaScript是世界上唯一一门不需要实例化，就可以直接调用构造函数（"Class"）的编码语言。因此，类和函数的构造函数由采用UpperCamelCase开始区分。
### 代码示例
```
  // 使用UpperCamelCase命名类名
  class SomeClassExample () { 
    
    // 常量使用const关键字，并使用lowerCamelCase命名
    const config = {
      key: 'value'
    };
    
    // 变量和方法使用lowerCamelCase命名
    let someVariableExample = 'value';
    function doSomething() {
      
    }
  }
```
## 3.3 先 require, 而不是在方法内部
定义: 在每个文件的起始位置，在任何函数的前面和外部require模块。这种简单的最佳实践，不仅能帮助您轻松快速地在文件顶部辨别出依赖关系，而且避免了一些潜在的问题。

否则: 在Node.js中，require 是同步运行的。如果从函数中调用它们，它可能会阻塞其他请求，在更关键的时间得到处理。另外，如果所require的模块或它自己的任何依赖项抛出错误并使服务器崩溃，最好尽快查明它，如果该模块在函数中require的，则可能不是这样的情况。

**错误代码示例**
```
function myClassFunction () {
  const _ = require('lodash')
  
  return _.merge({name: 'class'})
}
```
**正确代码示例**
```
const _ = require('lodash')
function myClassFunction () {

  return _.merge({name: 'class'})
}
```
## 
## 3.4 在文件夹上 require ，而不是直接在文件上
定义: 当在一个文件夹中开发库/模块，放置一个文件index.js暴露模块的 内部，这样每个消费者都会通过它。这将作为您模块的一个接口，并使未来的变化简单而不违反规则。

否则: 更改文件内部结构或签名可能会破坏与客户端的接口。
### 代码示例
```
  // 建议
  module.exports.SMSProvider = require('./SMSProvider');
  module.exports.SMSNumberResolver = require('./SMSNumberResolver');

  // 避免
  module.exports.SMSProvider = require('./SMSProvider/SMSProvider.js');
  module.exports.SMSNumberResolver = require('./SMSNumberResolver/SMSNumberResolver.js');
```


3. 测试和总体质量实践
## 4.1 至少，编写API（组件）测试
定义: 大多数项目只是因为时间表太短而没有进行任何自动化测试，或者测试项目失控而正被遗弃。因此，优先从API测试开始，这是最简单的编写和提供比单元测试更多覆盖率的事情（你甚至可能不需要编码而进行API测试，像[Postman](https://www.getpostman.com/)。之后，如果您有更多的资源和时间，继续使用高级测试类型，如单元测试、DB测试、性能测试等。

否则: 您可能需要花很长时间编写单元测试，才发现只有20%的系统覆盖率。

## 4.2 使用一个linter检测代码问题
定义: 使用代码linter检查基本质量并及早检测反模式。在任何测试之前运行它, 并将其添加为预提交的git钩子, 以最小化审查和更正任何问题所需的时间。也可在[Section 3](https://github.com/i0natan/nodebestpractices#3-code-style-practices)中查阅编码样式实践

否则: 您可能让一些反模式和易受攻击的代码传递到您的生产环境中。

**代码示例（**[husky](https://github.com/typicode/husky)**）**

```
 {
  "husky": {
    "hooks": {
      "pre-commit": "standard && yarn test"
    }
  }
```

## 4.3 经常检查易受攻击的依赖
定义: 即使是那些最有名的依赖模块，比如Express，也有已知的漏洞。使用社区和商业工具，比如 🔗 [nsp](https://github.com/nodesecurity/nsp) ，集成在您的CI平台上，在每一次构建的时候都会被调用，这样可以很容易地解决漏洞问题。

否则: 在没有专用工具的情况下，使代码清除漏洞，需要不断地跟踪有关新威胁的在线出版物，相当繁琐。

## 4.4 检查过期的依赖包
定义: 使用您的首选工具 (例如 “npm outdated” or [npm-check-updates](https://github.com/tjunnone/npm-check-updates) 来检测已安装的过期依赖包, 将此检查注入您的 CI 管道, 甚至在严重的情况下使构建失败。例如, 当一个已安装的依赖包滞后5个补丁时 (例如:本地版本是1.3.1 的, 存储库版本是1.3.8 的), 或者它被其作者标记为已弃用, 可能会出现严重的情况 - 停掉这次构建并防止部署此版本。

否则: 您的生产环境将运行已被其作者明确标记为有风险的依赖包

4. 进入生产实践
## 5.1. 监控!
定义: 监控是一种在顾客之前发现问题的游戏 – 显然这应该被赋予前所未有的重要性。考虑从定义你必须遵循的基本度量标准开始（我的建议在里面），到检查附加的花哨特性并选择解决所有问题的解决方案。市场已经淹没其中。点击下面的 ‘The Gist’ ，了解解决方案的概述。

否则: 错误 === 失望的客户. 非常简单.
### 监控示例：StackDriver默认仪表板。很难提取应用内指标
[null](https://github.com/i0natan/nodebestpractices/blob/master/assets/images/monitoring2.jpg)
### 
### 监控示例：Grafana作为可视化原始数据的UI层
[null](https://github.com/i0natan/nodebestpractices/blob/master/assets/images/monitoring3.png)

## 5.2. 使用智能日志增加透明度
定义: 日志可以是调试语句的一个不能说话的仓库，或者表述应用运行过程的一个漂亮仪表板的驱动。从第1天计划您的日志平台：如何收集、存储和分析日志，以确保所需信息（例如，错误率、通过服务和服务器等完成整个事务）都能被提取出来。

否则: 您最终像是面对一个黑盒，不知道发生了什么事情，然后你开始重新写日志语句添加额外的信息。

### 一段解释
无论如何，您要打印日志，显然需要一些可以在其中跟踪错误和核心指标的接口来包装生产环境信息（例如，每小时发生了多少错误，最慢的API节点是哪一个）为什么不在健壮的日志框架中进行一些适度的尝试呢? 要实现这一目标，需要在三个步骤上做出深思熟虑的决定:
1. 智能日志 – 在最基本的情况下，您需要使用像[Winston](https://github.com/winstonjs/winston), [Bunyan](https://github.com/trentm/node-bunyan)这样有信誉的日志库，在每个事务开始和结束时输出有意义的信息。还可以考虑将日志语句格式化为JSON，并提供所有上下文属性（如用户id、操作类型等）。这样运维团队就可以在这些字段上操作。在每个日志行中包含一个唯一的transaction ID，更多的信息查阅条款 “Write transaction-id to log”。最后要考虑的一点还包括一个代理，它记录系统资源，如内存和CPU，比如Elastic Beat。

2. 智能聚合 – 一旦您在服务器文件系统中有了全面的信息，就应该定期将这些信息推送到一个可以聚合、处理和可视化数据的系统中。例如，Elastic stack是一种流行的、自由的选择，它提供所有组件去聚合和产生可视化数据。许多商业产品提供了类似的功能，只是它们大大减少了安装时间，不需要主机托管。

3. 智能可视化 – 现在的信息是聚合和可搜索的, 一个可以满足仅仅方便地搜索日志的能力, 可以走得更远, 没有编码或花费太多的努力。我们现在可以显示一些重要的操作指标, 如错误率、平均一天CPU使用, 在过去一小时内有多少新用户选择, 以及任何其他有助于管理和改进我们应用程序的指标。
### 可视化示例: Kibana(Elastic stack的一部分)促进了对日志内容的高级搜索
[null](https://github.com/i0natan/nodebestpractices/blob/master/assets/images/smartlogging1.png)
### 
### 可视化示例: Kibana(Elastic stack的一部分)基于日志来可视化数据
[null](https://github.com/i0natan/nodebestpractices/blob/master/assets/images/smartlogging2.jpg)


## 5.3 锁住依赖
定义: 您的代码必须在所有的环境中是相同的，但是令人惊讶的是，NPM默认情况下会让依赖在不同环境下发生偏移 – 当在不同的环境中安装包的时候，它试图拿包的最新版本。克服这种问题可以利用NPM配置文件， .npmrc，告诉每个环境保存准确的（不是最新的）包的版本。另外，对于更精细的控制，使用NPM “shrinkwrap”。*更新：作为NPM5，依赖默认锁定。新的包管理工具，Yarn，也默认锁定。

否则: QA测试通过的代码和批准的版本，在生产中表现不一致。更糟糕的是，同一生产集群中的不同服务器可能运行不同的代码。

### 一段解释
您的代码依赖于许多外部包，假设它“需要”和使用momentjs-2.1.4，默认情况下，当布署到生产中时，npm可能会获得momentjs 2.1.5，但不幸的是，这将带来一些新的bug。使用npm配置文件和设置 –save-exact=true 指示npm去完成安装，以便下次运行 npm install（在生产或在Docker容器中，您计划将其用于测试）时，将获取相同的依赖版本。另一种可选择受欢迎的方法是使用一个shrinkwrap文件（很容易使用npm生成）指出应该安装哪些包和版本，这样就不需要环境来获取新版本了。
* 更新: 在npm5中，使用.shrinkwrap依赖项会被自动锁定。Yarn，一个新兴的包管理器，默认情况下也会锁定依赖项。
### 代码示例: .npmrc文件指示npm使用精确的版本
```
// 在项目目录上保存这个为.npmrc 文件
save-exact:true
```

### 
### 代码示例: shirnkwrap.json文件获取准确的依赖关系树
```
{
    "name": "A",
    "dependencies": {
        "B": {
            "version": "0.0.1",
            "dependencies": {
                "C": { 
                    "version": "0.1.0"
                }
            }
        }
    }
}
```


### 
### 代码示例: npm5依赖锁文件 - package.json
```
{
    "name": "package-name",
    "version": "1.0.0",
    "lockfileVersion": 1,
    "dependencies": {
        "cacache": {
            "version": "9.2.6",
            "resolved": "https://registry.npmjs.org/cacache/-/cacache-9.2.6.tgz",
            "integrity": "sha512-YK0Z5Np5t755edPL6gfdCeGxtU0rcW/DBhYhYVDckT+7AFkCCtedf2zru5NRbBLFk6e7Agi/RaqTOAfiaipUfg=="
        },
        "duplexify": {
            "version": "3.5.0",
            "resolved": "https://registry.npmjs.org/duplexify/-/duplexify-3.5.0.tgz",
            "integrity": "sha1-GqdzAC4VeEV+nZ1KULDMquvL1gQ=",
            "dependencies": {
                "end-of-stream": {
                    "version": "1.0.0",
                    "resolved": "https://registry.npmjs.org/end-of-stream/-/end-of-stream-1.0.0.tgz",
                    "integrity": "sha1-1FlucCc0qT5A6a+GQxnqvZn/Lw4="
                }
            }
        }
    }
}
```
# 

