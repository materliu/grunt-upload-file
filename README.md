# grunt-upload-file

> After we build the project, sometimes we may need to upload some files through form post method !

## Getting Started
This plugin requires Grunt.

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use Grunt plugins. Once you're familiar with that process, you may install this plugin with this command:

```shell
npm install grunt-upload-file --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this line of JavaScript:

```js
grunt.loadNpmTasks('grunt-upload-file');
```

## The "upload_file" task

### Overview
In your project's Gruntfile, add a section named `upload_file` to the data object passed into `grunt.initConfig()`.

_Run this task with the `grunt upload_file` command._

This task was designed to be used in conjunction with one background management system. In Tencent.com when we publish our static resources to the cdn or webserver, we need go to the background management system to publish one zip file! What the zip file for? We develop our app ,for examples wechat/mobile qq, by hybrid model! The 2g, 3g network connection is very slow, so users have to wait before the browser gets the web resources when he first open the related webview in the app. So when our app found the users' network connection is wifi, it does web resources prefetching by downloading the related updated zip containing the webview needed resources! when the users open the webview, if the related zip is there, no static resources network requests will be sent, the app simply offer the prefetched files contained in zip.

```js
grunt.initConfig({
  upload_file: {
    options: {
      // Task-specific options go here.
    },
    your_target: {
      // Target-specific file lists and/or options go here.
    },
  },
})
```

### Options

#### options.separator
Type: `String`
Default value: `',  '`

A string value that is used to do something with whatever.

#### options.punctuation
Type: `String`
Default value: `'.'`

A string value that is used to do something else with whatever else.


### Options

grunt-upload-file uses the [request](https://github.com/mikeal/request) module under the hood, and apart from a couple specific to grunt-upload-file options, the rest get passed straight to it. Here's a copy of the of the module's option docs. Otherwise, [go to the repo](https://github.com/mikeal/request) and have a look at what's it's capable of.

- `uri || url` - fully qualified uri or a parsed url object from url.parse(). REQUIRED.
- `callback` - A function that will receive an `error`, `response` and `body`, after a response if finished
- `qs` - object containing querystring values to be appended to the uri
- `method` - http method, defaults to GET
- `headers` - http headers, defaults to {}
- `body` - entity body for PATCH, POST and PUT requests. Must be buffer or string.
- `sourceField` - A field in the body or form to add the source files' contents to. Can contain full stops to separate object path. Ie "form.js\_code".
- `auth` - A hash containing values user || username, password || pass, and sendImmediately (optional). [See more info here](https://github.com/mikeal/request#http-authentication).
- `json` - sets body but to JSON representation of value and adds Content-type: application/json header. Additionally, parses the response body as json.
- `multipart` - (experimental) array of objects which contains their own headers and body attribute. Sends multipart/related request. See example below.
- `followRedirect` - follow HTTP 3xx responses as redirects. defaults to true.
- `followAllRedirects` - follow non-GET HTTP 3xx responses as redirects. defaults to false.
- `maxRedirects` - the maximum number of redirects to follow, defaults to 10.
- `encoding` - Encoding to be used on setEncoding of response data. If set to null, the body is returned as a Buffer.
- `pool` - A hash object containing the agents for these requests. If omitted this request will use the global pool which is set to node's default maxSockets.
- `pool.maxSockets` - Integer containing the maximum amount of sockets in the pool.
- `timeout` - Integer containing the number of milliseconds to wait for a request to respond before aborting the request
- `proxy` - An HTTP proxy to be used. Support proxy Auth with Basic Auth the same way it's supported with the url parameter by embedding the auth info in the uri.
- `oauth` - Options for OAuth HMAC-SHA1 signing. [See more info here](https://github.com/mikeal/request#oauth-signing). The `oauth-sign` module must be installed to use this functionality.
- `hawk` - Options for [Hawk signing](https://github.com/hueniverse/hawk). The credentials key must contain the necessary signing info, [see hawk docs for details](https://github.com/hueniverse/hawk#usage-example). You will need to install the `hawk` module to use this functionality.
- `strictSSL` - Set to true to require that SSL certificates be valid. Note: to use your own certificate authority, you need to specify an agent that was created with that ca as an option.
- `jar` - If true, remember cookies for future use (or define your custom cookie jar; [see mikeal/request's examples](https://github.com/mikeal/request#examples)). To get either of these functions working you'll need to install an optional dependecy `npm i tough-cookie`.
- `aws` - object containing aws signing information, should have the properties key and secret as well as bucket unless you're specifying your bucket as part of the path, or you are making a request that doesn't use a bucket (i.e. GET Services). You will need to install the `aws-sign2` module to use this functionality.
- `httpSignature` - Options for the [HTTP Signature Scheme](https://github.com/joyent/node-http-signature/blob/master/http_signing.md) using [Joyent's library](https://github.com/joyent/node-http-signature). The `http-signature` module must be installed and the keyId and key properties must be specified.
- `localAddress` - Local interface to bind for network connections.
- `ignoreErrors` - Ignore the status code returned (if any).

There are a few optional dependencies you'll need to install to get certain functionality from this module.

- if you wish to use cookies (`jar`) install `tough-cookie`
- if you want to pass `multipart/form-data` you'll need to install `form-data`
- if you wish to tunnel your requests install `tunnel-agent`
- if you want to use Joyent's HTTP Signature Scheme, install `http-signature`
- if you require oauth signing, you need to install the `oauth-sign` module
- to use Hawk signing, you must use the `hawk` module
- if you want to use AWS signing, you must install the `aws-sign2` module

### Usage examples

```javascript
// Project configuration.
grunt.initConfig({
    upload_file: {
        "zip-partly": {    // only the special QQ num user (usually the developer or tester) can be noticed the zip file has been updated !
            src: ['dist/qqcache/*.zip'],
            options: {
                url: 'http://admin.connect.oa.com/modules/public/uploadOfflinePackage.do',
                method: 'POST',
                paramObj: {
                    'publish': true,    // 这个参数用来告诉cgi，创建包的同时是否需要发布
                    'compatible': 0,    // 兼容版本
                    'bid': 118,    // 所属业务
                    'version': 0,    // 手Q版本号
                    'platform': [2, 3],    // 支持平台， 2， 3表示ios android平台都都勾选上了
                    'loadmode': 2,    // 加载模式， 2表示拦截加载
                    'frequency': 1,    // 更新检测的时间频率， 1表示1分钟检测一次
                    'gray': true,    // 是否灰度， true表示灰度
                    'uins': [
                        {"min":2568612250,"max":2568612250},
                        {"min":644323349,"max":644323349},
                    ]    // 要灰度的QQ号
                }
            }
        },
        "zip-totally": {
            src: ['dist/qqcache/*.zip'],
            options: {
                url: 'http://admin.connect.oa.com/modules/public/uploadOfflinePackage.do',
                method: 'POST',
                paramObj: {
                    'publish': true,    // 这个参数用来告诉cgi，创建包的同时是否需要发布
                    'compatible': 0,    // 兼容版本
                    'bid': 118,    // 所属业务
                    'version': 0,    // 手Q版本号
                    'platform': [2, 3],    // 支持平台， 2， 3表示ios android平台都都勾选上了
                    'loadmode': 2,    // 加载模式， 2表示拦截加载
                    'frequency': 1,    // 更新检测的时间频率， 1表示1分钟检测一次
                    'gray': false    // 是否灰度， true表示灰度, false，不灰度，全量外网用户
                }
            }
        }
    },
});


grunt.registerTask('default', [
    'jshint',
    'test',
    'build',
    'upload_file:zip-partly'
]);

```

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).

## Release History
* 2012-04-16   v0.0.1   Work in progress, not yet officially released.

## About
grunt-upload-file is an open-source project by [Tencent](http://www.tencent.com/en-us/) which builds on top of [Node.js](https://nodejs.org).

## Used by people within <a href="https://github.com/materliu/grunt-localhosts/issues/">(JOIN US)</a>
![Tencent](http://upload.wikimedia.org/wikipedia/commons/thumb/2/22/Tencent_Logo.svg/200px-Tencent_Logo.svg.png)

## License
Copyright (c) 2014 materliu. Licensed under the MIT license.

---

Task submitted by [materliu](http://materliu.github.com)

*This file was generated on Fri Apr 16 2014 22:38:50.*
