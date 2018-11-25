---
title: 如何用 Node.js Cluster，监听异常的邮件提醒服务
date: 2017-06-11 22:24:57
categories:
  - 开发
tags: 
  - Node.js
  - Cluster
---

```bash
                    __                __           __                                       _ __
   ____  ____  ____/ /__        _____/ /_  _______/ /____  _____      ___  ____ ___  ____ _(_) /
  / __ \/ __ \/ __  / _ \______/ ___/ / / / / ___/ __/ _ \/ ___/_____/ _ \/ __ `__ \/ __ `/ / /
 / / / / /_/ / /_/ /  __/_____/ /__/ / /_/ (__  ) /_/  __/ /  /_____/  __/ / / / / / /_/ / / /
/_/ /_/\____/\__,_/\___/      \___/_/\__,_/____/\__/\___/_/         \___/_/ /_/ /_/\__,_/_/_/

```
[![Build Status](https://travis-ci.org/sunNode/node-cluster-email.svg?branch=master)](https://travis-ci.org/sunNode/node-cluster-email)


A module for taking advantage of the built-in cluster module in node v0.8 and above.send email if cluster exception,you will recieve a detail email about worker exception.

## Installation
```bash
npm install https://github.com/sunNode/node-cluster-email
```


## Usage
Initialize mail plugin with the given email(s), with the given options.

 Options:

  - `from` sender email
  - `timeout` sendmail timeout in milliseconds
  - `subject` defaulting to "cluster({worker}) exception: {message}"
  - `template` function called with local variables (usually jade / ejs template etc)

## Example

```bash
const cluster = require('cluster')
const cpu = require ('os').cpus().length
const mail = require('node-cluster-mail')

/**
 * [if description]
 * @param  {[type]} cluster.isMaster [description]
 * @return {[type]}                  [description]
 */
if (cluster.isMaster) {
  // Fork workers.
  for (let i = 0; i < cpu; i++) {
    cluster.fork();
  }

  cluster.on('exit', function(worker, code, signal) {
    mail('phonenix@gmail.com',{'from':'phonenix-test@gmail.com','cc':'phonenix-test2@gmail.com'})
    console.log('worker ' + worker.process.pid + ' died');
  });

  cluster.on('fork', function(worker) {
  console.log(`workers: ${worker.id} worker.process.pid :${worker.process.pid}`)
});
} else {
  // Workers can share any TCP connection
  // In this service its a restify server
  require('./app')
}
```

# Reference
[node-cluster-email](https://github.com/sensitiveMix/node-cluster-email)
