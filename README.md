
# Unofficial loopback-testing

**Utilities for testing LoopBack apps**

[![Build Status](https://travis-ci.org/iGLOO-be/loopback-testing.svg?branch=master)](https://travis-ci.org/iGLOO-be/loopback-testing)

## Unofficial

Strongloop has [deprecated](https://github.com/strongloop/loopback-testing/commit/bf393b4cc822b0a51420f1d2e74f137882d775b1) the original loopback-testing.
This repository is a fork made by [iGLOO](http://igloo.be). Feel free to contribute!

## overview

The following helpers are designed to generate [mocha] tests against
[LoopBack](http://strongloop.com/loopback) apps.

## install

1. `npm install loopback-testing --save-dev`
2. Assuming you started with a clean template/project generated by `slc loopback`
  1. If you have mocha installed as a global npm module that's great! Simply update `<your_project>/package.json` with:

    ```
    {
      ...
      "scripts": {
        ...
        "test": "mocha"
      }
    }
    ```
  2. Otherwise, you can utilize the mocha library within the `loopback-testing` testing module:

    ```
    {
      ...
      "scripts": {
        ...
        "test": "./node_modules/loopback-testing/node_modules/.bin/mocha"
      }
    }
    ```
3. Run `npm test` to execute any tests under the `test` directory.

## basic usage

Below is a simple LoopBack app.

```js
var loopback = require('loopback');
var app = loopback();
var Product = app.model('product');
Product.attachTo(loopback.memory());
```

Use the `loopback-testing` module to generate `mocha` tests.

```js
var lt = require('loopback-testing');
var assert = require('assert');
var app = require('../server/server.js'); //path to app.js or server.js

describe('/products', function() {
  lt.beforeEach.withApp(app);
  lt.describe.whenCalledRemotely('GET', '/products', function() {
    lt.it.shouldBeAllowed();
    it('should have statusCode 200', function() {
      assert.equal(this.res.statusCode, 200);
    });

    lt.beforeEach.givenModel('product');
    it('should respond with an array of products', function() {
      assert(Array.isArray(this.res.body));
    });
  });
});
```

## building test data

Use TestDataBuilder to build many Model instances in one async call. Specify
only properties relevant to your test, the builder will pre-fill remaining
required properties with sensible defaults.

```js
var TestDataBuilder = require('loopback-testing').TestDataBuilder;
var ref = TestDataBuilder.ref;

// The context object to hold the created models.
// You can use `this` in mocha test instead.
var context = {};

var ref = TestDataBuilder.ref;
new TestDataBuilder()
  .define('application', Application, {
    pushSettings: { stub: { } }
  })
  .define('device', Device, {
     // use the value of application's id
     // the value is resolved at build time
     appId: ref('application.id'),
     deviceType: 'android'
  })
  .define('notification', Notification)
  .buildTo(context, function(err) {
    // test models are available as
    //   context.application
    //   context.device
    //   context.notification
  });
```
