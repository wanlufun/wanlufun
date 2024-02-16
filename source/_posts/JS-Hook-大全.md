---
title: JS Hook 大全
tags:
  - JavaScript
cover: 'https://images.wanlu.fun/picgo/202402162015014.png'
abbrlink: 41877ffd
date: 2024-02-16 20:13:44
---

> JS 变量是有作用域的，只有当被 hook 函数和 debugger 断点在同一个作用域的时候，才能 hook 成功。


1. 过各种debugger断点（控制台反调试）
2. 拦截请求
3. 注入ws 
4. 主动debugger，用户请求参数加密（用的最多）
5. 函数替换，函数置空，函数覆盖（过debugger有时候用）



### JSON.parse()

```javascript
(function () {
  var parse_ = JSON.parse;
  JSON.parse = function (arg) {
    console.log(arg);
    debugger;
    return parse_(arg);
  };
})();
```



### XMLHttpRequest（**请求的参数**）

```javascript
(function () {
  var _open = window.XMLHttpRequest.prototype.open;
  window.XMLHttpRequest.prototype.open = function (method, url, async) {
    if (url.indexOf("参数名称") != -1) {
      debugger;
    }
    return _open.apply(this, arguments);
  };
})();
```



### XMLHttpRequest（header）

```javascript
(function () {
  var _setRequestHeader = window.XMLHttpRequest.prototype.setRequestHeader;
  window.XMLHttpRequest.prototype.setRequestHeader = function (key, value) {
    if (key == "header 的参数 key") {
      debugger;
    }
    return open.apply(this, arguments);
  };
})();
```



### Cookie

```javascript
(function() {
  var cookieTemp = '';
  Object.defineProperty(document, 'cookie', {
    set: function (val) {
      if (val.indexOf('cookie 关键字') != -1) {
        debugger;
      }
      console.log(val);
      cookieTemp = val;
      return val;
    },
    get: function() {
      return cookieTemp;
    },
  });
})();
```



### eval

```javascript
(function() {
  var _eval = eval;
  eval = function(val) {
    if (val.indexof('debugger') === -1) {
      _eval_cache(obj);
    }
  }
})();
```



### 方法置空（过 debugger）

```javascript
// 原理
function main() {
  debugger;
}

main = function() {}; // 置空
```



### 无限 debugger

> 原理：debugger 关键字。
> 常常利用 javascript 构造出 debugger 使用 Function.constructor / setInterval / eval 。
> 一般相关代码会被混淆。



#### eval

```javascript
// 通常以下代码是加密构造出来的， eval 执行的是一个字符串变量。
eval("debugger;")
eval("(function() {var a = new Date(); debugger; return new Date() - a > 100;}())")

1.使用字符串 replace("debugger;","        ") 方法，进行替换 debugger ，为防止对字符串进行长度
  检测，常常使用等长的空格替换
2.Hook
  (function() {
    var _eval = eval;
    eval = function(val) {
      if (val.indexof('debugger') === -1) {
        _eval_cache(obj);
      }
    }
  })();
3.置空
  eval = function() {};
```



#### 定时器

```javascript
// 对于定时器
setInterval(function () { debugger; }, 500);


1. 方法置空
  setInterval = function() {};
```



#### Function

```javascript
// 常见形式
Function('debugger')()
(function(){return false;})['constructor']('debugger')['call']();
xxx.constructor("debugger").call("action")

1. Hook
Function.prototype.__constructor_back = Function.prototype.constructor;
Function.prototype.constructor = function() {
  if(arguments && typeof arguments[0]==='string'){
    //alert("new function: "+ arguments[0]);
    if("debugger" === arguments[0]){
      //arguments[0]="console.log(\"anti debugger\");";
      //arguments[0]=";";
      return;
    }
  }
 return Function.prototype.__constructor_back.apply(this,arguments);
}
```



