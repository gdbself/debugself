---
title: JavaScript中Promise/async/await的用法和原理
tags:
  - async
  - await
  - JavaScript
  - Promise
  - 异步
url: 160.html
id: 160
categories:
  - JavaScript
keywords: JavaScript,Promise,async,await,JS异步,异步的原理
description: 'Promise是什么,JavaScript异步的实现原理,Promise/async/await的用法教程'
date: 2018-04-11 01:03:19
---

## JavaScript中Promise是什么
**先说结论：promise本质是对回调函数的封装！**

那么直接使用js的回调函数不行吗，promise存在的意义是什么？

因为使用回调函数很容易形成回调函数的嵌套，举个栗子，文件fileA读取完毕后，才能读取文件fileB，而文件fileB读取完毕后，才能读取文件fileC，伪代码如下
```
fs.readFile("fileA", function (err, data) {
     fs.readFile("fileB", function (err, data) {
              fs.readFile("fileC", function (err, data) {
              })
     })
});
```
上面的代码只是嵌套了三层，当业务逻辑更复杂时，嵌套N层后，代码将很难维护！

如何使用promise如何避免回调函数的嵌套呢，一般promise要和async+await配套使用（后面会说明async+await的原理和用法），代码如下
```
const fs = require('fs');
function myReadFile(file) {
    return new Promise(function (accept, reject) {
        fs.readFile(file, function (err, data) {
        if(err){reject(err);}
        else if(data){console.log(data);accept(data);});
    });
}
async function test(){
    await myReadFile("fileA");
    await myReadFile("fileB");
    await myReadFile("fileC");
}
test();
```
上述代码中，myReadFile返回的是一个Promise对象，Promise对象内部依旧调用了fs.readFile，Promise内部只是对fs.readFile进行了封装。

await myReadFile("fileA")，表示当fileA没有读取完毕，就一直wait，直到fileA读取完毕，只有fileA读取完毕后，才会执行await  myReadFile("fileB")；

同理，只有fileB读取完毕后，才会执行await  myReadFile("fileC")；

## Promise/async/await的内部原理

await内部是如何实现等待或继续执行的功能呢？这就引出Promise的状态了，Promise有三种状态：

* Pending（进行中）
* Resolved（已完成，又称 Fulfilled）
* Rejected（已失败）
    

新建一个Promise后，Promise的初始状态就是Pending；

我们看下构造Promise时的参数
```
Promise(function (accept, reject) {})
```
accept和reject由系统构造，这两个参数由系统传递给Promise，当Promise内部封装的回调函数fs.readFile执行完毕后：

* 当读取发生错误时，我们调用reject，此时Promise进入Rejected状态；  
* 当读取正确时，我们调用accept，此时Promise进入Resolved状态；
    

我们知道，JS的本质是靠一个单线程的事件循环（不明白JS事件循环的，请自行查资料），不停的从任务队列中取出任务并执行，**当JS的事件循环执行到await时，会检查Promise的状态，根据Promise的状态，然后决定是等待，还是继续执行**，具体如下：

* 当发现状态是Pending，就暂停当前任务的执行（即不执行await后面的代码，虽然await后面的代码被阻塞了，但是事件循环并不会阻塞，它会继续循环，执行任务队列中的其他任务，这样不浪费CPU，这也是JS实现异步函数的原理）
* 当发现状态是Rejected或者Resolved，说明Promise已经执行完毕，就继续执行当前任务（即await后面的代码）
    
综上所述，JS的事件循环，不停的获取Promise的状态，根据状态的不同，决定等待还是继续执行，这其实就是个状态机啦。

## Promise/async/await的用法举例

mocha是常用的单元测试框架，测试时通过断言如assert.equal(ret,true)来判断测试是否通过；

我们使用mocha测试MQTT.js，js连接mqtt服务器的代码如下
```
var mqtt = require('mqtt')
var client = mqtt.connect('mqtt://test.mosquitto.org')
client.on('connect', function () {
     console.log(‘connect successed’);
});
client.on('error', function () {
     console.log(‘connect failed’);
})
```
MQTT.js提供的mqtt.connect是异步函数，connect返回后，并不知道是否连接成功，必须通过client.on('connect')注册监听函数，当收到'connect'事件后，才能确定连接成功。上面的代码是不能直接放到mocha中，因为mocha断言时必须知道确定结果，比如下面的代码
```
const mqtt = require('mqtt');
const assert = require('assert');
describe.only('mqttTest', function () {
    it('login success', async function () {
        let mqtt_client = mqtt.connect({port: 1883, host: "192.168.120.128",username: 'testuser', password: "psw"});
        mqtt_client.on('connect', function () {assert.ok(true);});
        mqtt_client.on('error', function () {assert.ok(false);});
     });
});
```
上面代码中，因为mqtt_client.on对于的事件是异步的，当测试用例可能很快执行完毕时，connect和error可能还没有发生呢，所以assert可能根据就没有执行。我们使用Promise/async/await封装上面异步函数，代码如下
```
async function connect() {
    let mqtt_client = mqtt.connect({port: 1883, host: "192.168.120.128",username: 'testuser', password: "psw"});
    return new Promise(function (accept, reject) {
         // 在mqtt_client.on事件中，修改Promise的状态
        mqtt\_client.on('connect', function () {accept({result:true,value:mqtt\_client}); });
        mqtt\_client.on('error', function () {accept({result:false,value:mqtt\_client}); });
    });
}
describe.only('TempTest', function () {
    it('login success', async function () {
        let ret = await connect();
        assert.equal(ret.result, true);
    });
});
```
## Promise/async/await注意事项

* await关键字只能在async函数中才能使用，非async函数中会执行错误  
* await关键字后面必须跟promise对象，如果不是，会被转成一个立即resolve的 Promise 对象。
* 函数执行到await后，Promise函数执行完毕，但因为Promise内部一般都是异步函数，所以事件循环会一直wait，直到事件轮询检查到Promise有了状态resolve或reject 才重新执行这个函数后面的内容。
* async函数返回一个 Promise对象，且必须等到内部所有await命令后面的 Promise 对象执行完，async返回的Promise才会发生状态改变，除非遇到return语句或者抛出错误。
* 当promise函数内调用accept，await顺序执行，取得返回值，返回值即accept的参数当promise函数内调用reject，那就会在catch里面执行代码，catch的参数即reject的参数
```
try {
  const t = await f();
  console.log(t);
} catch (err) {
  console.log(err);
}
```