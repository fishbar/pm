[![Build Status](https://secure.travis-ci.org/aleafs/pm.png?branch=pm2.0)](http://travis-ci.org/aleafs/pm)

## About

`pm` 是一个轻量级的Node.js多进程管理器，基于之前的`node-cluster`重构而来，在淘宝内部的生产系统中得到了广泛的应用.

* 基于 master + worker 模式，master负责进程管理，worker 处理业务逻辑，有效利用现代服务器的多CPU;
* 同一 master 可管理多种类型的worker, 并且支持在不同类型的 worker 之间进行轻量的消息传递;
* 同一类型的 worker ，对于TCP请求，采用抢占式的方式进行负载均衡；
* 平滑退出和 不退出前提下的 worker 进程重载 (reload).

## Api

* Visit the [wiki page](wiki) to get more infomation about `pm`.
* Alse, we have demo scripts in the code directory [demo](demo).

## Install

```bash
$ npm install pm@2.0.0-alpha
```

## Benchmark

```bash

$ siege -b -c100 -t 60S http://172.0.0.2:33749/
```

* QPS (only one child, http server, response req.url) (node0.6.17):

<table>
  <tr>
    <th>CASE</th><th>Closed</th><th>KeepAlive</th>
  </tr>
  <tr>
    <td>pm2.0</td><td>5600</td><td>10553</td>
  </tr>
  <tr>
    <td>pm1.0</td><td>5231</td><td>10388</td>
  </tr>
  <tr>
    <td>node</td><td>5481</td><td>10126</td>
  </tr>
</table>

## Usage

* in `master.js`, run as master:

```javascript
var app = require('pm').createMaster({
 'pidfile' : '/tmp/demo.pid',
});

app.register('group1', __dirname + '/http.js', {
 'listen' : [8080, 8081]
});

app.on('giveup', function (name, num, pause) {
  // YOU SHOULD ALERT HERE!
});
app.dispatch();

```

* in `http.js`, run as worker:

```javascript
var http = require('http').createServer(function (req, res) {
 res.end('hello world');
});

require('pm').createWorker().ready(function (socket, port) {
 http.emit('connection', socket);
});
```

## Test

unit test

```bash
$ make test
```

jscoverage: [**99%**](http://aleafs.github.com/coverage/pm2.html)

```bash
$ make cov
```

## Contributors

Thanks goes to the people who have contributed code to this module, see the [GitHub Contributors page](https://github.com/aleafs/pm/graphs/contributors).

Below is the output from `git-summary`

```
 project: pm
 commits: 239
 files  : 23
 authors: 
   43	aleafs                  100.0%

```

## License

`pm` is published under MIT license.
See license text in [LICENSE](https://github.com/aleafs/pm/blob/master/LICENSE) file.

