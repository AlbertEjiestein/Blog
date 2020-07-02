+ babel搭建
+ babel-polyfill
+ babel-runtime

[非常详细的文章](https://segmentfault.com/a/1190000019718925)

### 环境搭建

+ .babelrc
  + preset是一个babel的plugin的集合，如下
  + @babel/preset-env，es6之后的语法转为es5，但不管API
  + @babel/preset-flow
  + @babel/preset-react，编译react的JSX使用
  + @babel/preset-typescript



### babel-polyfill

+ polyfill，补丁，是一块代码（通常是 Web 上的 JavaScript），用来为旧浏览器提供它没有原生支持的较新的功能。
+ core-js和regenerator（支持装饰器模式）插件
+ babel-polyfill是两者的集合，但babel7.4之后弃用



**babel只关心语法规范，不关心API的使用，因此需要使用babel-polyfill来实现对ES6 API的支持**

```js
import "@babel/polyfill"

const sum = (a, b) => a + b

Promise.resolve(100).then(data => data);

[10, 20, 30].includes(20)

----------------------------
// babel之后的代码
"use strict";

require("@babel/polyfill");

var sum = function sum(a, b) {
  return a + b;
};

Promise.resolve(100).then(function (data) {
  return data;
});
// [].abc() 是符合语法规范的
[10, 20, 30].includes(20);
```



**babel也不管模块化，因此需要配置webpack来完成模块化的使用**



**babel-polyfill按需引入，其实只用到了polyfill的corejs**

```js
// .babelrc
{
    "presets": [
        [
            "@babel/preset-env",
            {
                "useBuiltIns": "usage", // 按需加载
                "corejs": 3  // corejs版本为3
            }
        ]
    ],
    "plugins": [
       
    ]
}

------------------------------
"use strict";

require("core-js/modules/es.array.includes");
require("core-js/modules/es.object.to-string");
require("core-js/modules/es.promise");

var sum = function sum(a, b) {
  return a + b;
}; // 新的 API

Promise.resolve(100).then(function (data) {
  return data;
}); // 新的 API

[10, 20, 30].includes(20); 
```



### babel-runtime

+ babel-polyfill会污染全局变量，可以使用babel-runtime

```js
{
    "presets": [
        [
            "@babel/preset-env",
            // {
            //     "useBuiltIns": "usage",
            //     "corejs": 3
            // }
        ]
    ],
    "plugins": [
        [
            "@babel/plugin-transform-runtime",
            {
                "absoluteRuntime": false,
                "corejs": 3,
                "helpers": true,
                "regenerator": true,
                "useESModules": false
            }
        ]
    ]
}

------------------------------------------------
"use strict";

var _interopRequireDefault = require("@babel/runtime-corejs3/helpers/interopRequireDefault");

require("core-js/modules/es.object.to-string");

require("core-js/modules/es.promise");

var _includes = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/instance/includes"));

var _promise = _interopRequireDefault(require("@babel/runtime-corejs3/core-js-stable/promise"));

var _context;

var sum = function sum(a, b) {
  return a + b;
}; // 新的 API

_promise["default"].resolve(100).then(function (data) {
  return data;
}); // 新的 API

(0, _includes["default"])(_context = [10, 20, 30]).call(_context, 20);
```

