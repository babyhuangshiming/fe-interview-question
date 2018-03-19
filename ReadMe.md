# 整理前端面试题

## 1、盒模型

盒模型有标准W3C盒模型和怪异盒模型两种，根据box-sizing的值可以来设置是`W3C盒模型`还是`怪异盒模型`.

1. 属性为 content-box 为W3C 盒模型 它的内容为 content + padding + border + margin;

2. border-box 为怪异盒模型 它的内容为 content + margin (它的content 包括 content + padding + border)

## 2、3栏布局

1. 最好的方法  直接`flex`

2. 自适应部分们可以用`calc` 计算宽度，左右两个float正常布局就行

3. 注意一下双飞翼布局，中间的div再包裹一个子div  三个外部的div都用float布局

## 3、== 与 === 的区别

1. === 直接判断类型，在判断是否相等

2. == 会有隐式类型转换

- string/number/boolean/object  ---> (false) undefined/null

- string ---> number  string 要先转换成number

- boolean ---> number boolean 要先转换成number

- object ---> 先通过valueOf进行转换，如果为基本类型值则进行比较，否则再进行toString转换，如果还不相等，则进行number转换后比较

## 4、typeof的各种

注意一下 function 类型

- typeof null === 'object';
- typeof NaN === 'number'
- typeof undefined === 'undfined'

## 5、instanceof 的 原理

A instanceof B 的原理是:
查看对象B的prototype 属性指向的原型对象是否在对象A的原型链上，若在则返回true，若不在则返回false

简而言之就是 

`A.__proto__.__proto__.... = B.prototype;`

 `任何实例对象的__proto__ 都是指向其构造函数的prototype`  
 记住这句话，很重要！！！！
- __proto__ 隐式原型，作用是形成原型链
- prototype 显示原型，生成原型对象，方便所有实例，共享原型对象上的方法和属性

## 6、实现bind(使用apply、call)

``` javascript
Function.prototype.bind = function (context) {
    const handler = this;
    if (arguments.length > 2) {
        // call的那种形式
        const args = Array.prototype.slice.call(arguments, 1);
        return function () {
            return handler.apply(context, args);
        }
    } else {
        return function () {
            return handler.apply(context, arguments);
        }
    }
}
```

## 7、实现apply

``` javascript
Function.prototype.fakeApply = function (context, params = []) {
    // 指定建立对象
    const tempContext = context || window;
    // 建立该对象唯一的方法
    const fn = Symbol('fn');
    // 复制该方法到对象上
    tempContext[fn] = this;
    // 执行该方法
    tempContext[fn](...params);
    // 删除该方法
    delete tempContext[fn];
}
```

## 8、实现一个弹窗，使用单例模式

``` javascript
    var $ = function (id) {
        var dom = typeof id === 'string' ? document.getElementById(id) : id;
        return dom;
    }

    function Modal (id, content) {
        this.id = name;
        this.content = content;
        this.hasOpen = false;
    }

    Modal.prototype.create = function () {
        if (!this.hasOpen) {
            // 没有打开，创建
            var modal = document.createElement('div');
            modal.innerHtml = this.content;
            modal.id = this.id;
            document.body.appendChild(modal);
            setTimeout(function () {
                modal.classList.add('show');
            }, 0);
            this.open = true;
        }
    };

    Modal.prototype.close = function() {
        if (this.hasOpen) {
            var modal = $(this.id);
            modal.classList.add('hide');
            setTimeout(function () {
                document.body.removeChild(modal);
            }, 200);
            this.hasOpen = false;
        }
    };

    var createInstance = (function () {
        var instance;
        return function () {
            return instance || (instance = new Modal('modal', '这是一个弹窗'))
        }
    })();


    var operation = {
        setModal: null;
        open: function () {
            this.setModal = createInstance();
            this.setModal.create();
        },
        delete: function () {
            this.setModal ? this.setModal : '';
        }
    };
```

该题的关键点在于，用立即执行函数生成一个实例，保证一个构造函数只有一个实例

## 9、观察者模式

``` javascript
var Observer = {
    var _message = {};
    return {
        register: function (type, fn) {
            if (typeof _message[type] === 'undefined') {
                _message[type] = [fn];
            } else {
                _message[type].push(fn);
            }
        },
        fire: function (type, args) {
            if (!_message[type]) {
                return;
            }
            var events = {
                type: type,
                args: args || {}
            }
            for (var i = 0; i < _message[type].length; i++) {
                _message[type][i].call(this, events);
            }
        },

        remove: function (type, fn) {
            if (_message[type] instanceof Array) {
                var i = _message[type].length - 1;
                for(; i > 0; i--) {
                    _message[type].[i] === fn && _message[type].splice(i, 1);
                }
            }
        }
    }
}
```

## 10、继承

继承有四种
1. 原型继承:

    原型继承的缺点，子构造函数不能像父构造函数传递参数，原型上的引用类型值会被所有的实例所共享

``` javascript
var superType = function () {
    this.superName = 'supernName';
};
superType.prototype.getSuperName = function () {
    return this.superName;
};

var subType = function () {
    this.subName = 'subName';
}

subType.prototype = new superType();

subType.prototype.getSubType = function() {
    return this.subName;
};

var mySubTypeFunc = new subType();
mySubTypeFunc.getSubType();
```

2. 构造函数继承:

    所有的实例都会生成一个自己的副本，解决了原型上的引用类型被共享的问题，但是同时构造函数中的函数却因为每个实例都生成了一个副本导致不能复用，这是构造函数的缺点。

``` javascript
var superType = function (name) {
    this.name = name;
};


var subType = function (name, age) {
    superType.call(this, name);
    this.array = [1, 2, 3, 4, 5, 6];
    this.age = age;
}

// subType.prototype = new superType();
subType.prototype.getName = function () {
    return this.name;
}

var mySubType = new subType('黄小猛', 25);
mySubType.getName();
```

3. 组合继承:

    组合继承很好的解决了引用类型的值放在实例中，而需要被共享的函数放在原型上，组合继承的缺点在于，子构造函数需要调用两次父构造函数

``` javascript
var superType = function (name) {
    this.name = name;
};


var subType = function (name, age) {
    superType.call(this, name);
    this.array = [1, 2, 3, 4, 5, 6];
    this.age = age;
}

subType.prototype = new superType();
subType.prototype.getName = function () {
    return this.name;
}

var mySubType = new subType('黄小猛', 25);
mySubType.getName();
```
4. 继承式继承
    利用Object.create 很好的解决了，调用两次父构造函数的问题
``` javascript
 var superType = function (name) {
    this.name = name;
}

superType.prototype.getSuperName = function () {
    return this.name;
}

var subType = function (name) {
    superType.call(this, name);
    this.array = [1, 2, 3, 4, 5];
}

var inhrientObj = function (subObj, superObj) {
    var obj = Object.create(superObj.prototype);
    obj.constructor = subObj;
    subObj.prototype = obj;
}

inhrientObj(subType, superType);

var mySubType = new subType('黄小猛');
mySubType.getSuperName();
```

## 11、闭包 作用域 this

- 闭包 -》 闭包 = 函数 + 函数内部能访问的变量组成

    会在闭包里面嵌套一个函数是因为 要保护里面需要return 出来的局部变量，而不让全局随便对局部变量进行改动导致污染局部变量，所以需要再用一个函数把局部变量return出来，是全局
    环境中可以使用这个变量，而又不能污染这个局部变量。

- 作用域
    每一个函数执行时候都会有一个活动对象，如果我要查找一个变量，首先会在局部的作用域内查找，如果没有再一层层往外找，直到找到最外层的window作用域，这种查找的过程形成一个作用域链

- this
    - 非箭头函数的this是在运行时进行绑定的，并不是在编写的时候绑定的，他的上下文取决于函数调用时候的各种条件。

    - 箭头函数的this 是在定义函数的时候绑定的，箭头函数本身是没有this的，他的this就是写箭头函数的作用域。


 ### 12、BFC的原理

BFC是块级格式化上下文,他规定了内部的Block_level box 是如何布局的，并且一个BFC内部的区域是不会影响到这个外部区域的。

- BFC的布局规范
    1. 内部的box会在垂直方向，一个接一个的放置。

    2. Box垂直方向由margin决定，属于同一个BFC的两个相邻的box的margin会发生重叠（margin塌陷）。

    3. 每个元素的margin box的左边，与包含块的border-box左边相接触（即使存在浮动）。

    4. BFC区域不会与float box重叠。

    5. BFC就是页面上的一个隔离的独立容器，容器的子元素不会影响到外面的元素。

    6. 计算BFC高度时，浮动元素也参与计算。

- 哪些元素生成BFC
    1. 根元素

    2. float 不为 none

    3. position 为 fixed 或者 absolute

    4. display 为 inline-block、table-cell、table-caption、flex、inline-flex

    5. overflow 不为 visible

## 13、去浮动

闭合浮动

1. 给float的父元素增加

``` css
    .div :after {
        content: '';
        clear: both;
        height: 0;
        display: block;
        visibility: hidden
    }
```

2. 使父元素形成一个BFC，通过设置height, overflow, display等方法。

## 14、js防抖
```javascript
function debounce (fn, delay) {
    var ctx;
    var args;
    var timer = null;
    return function () {
        ctx = this;
        args = arguments;
        if (timer) {
            clearTimeout(timer);
            timer = null;
        }

        timer = setTimeout(function () {
            fn.apply(ctx, this);
            timer = null;
        }, delay);
    }
}
```

## 15、js节流
```javascript
function throttle (fn, delay) {
    var ctx;
    var args;
    var previousTime = Date.now();

    return function () {
        ctx = this;
        args = arguments;
        var nowTime = Date.now();
        var diff = nowTime - previousTime - delay;
        if (diff >= 0) {
            setTimeout(function () {
                fn.apply(ctx, args);
            }, delay);
            previousTime = nowTime;
        }
    }
}
```
## 16、实现一个promise
```javascript
// 简单的promise
function newPromise (fn) {
    var events = [];
    this.then = function (func) {
        events.push(func);
        return this;
    }

    function resolve(newValue) {
        var func = events.shift();
        func(newValue, resolve);
    }

    fn(resolve);
}

var myPromise = new newPromise(function (resolve) {
    console.log('get...');
    setTimeout(function () {
        console.log('get 1');
        resolve(1);
    }, 1000);
});

myPromise.then(function (value, resolve) {
    console.log('get...');
    setTimeout(function () {
        console.log('get 2');
        resolve(3);
    }, 1000);
}).then(function (value) {
    console.log(value);
});
```
一个简易版本，reject部分还没有实现

## 17、实现new
```javascript
    function _new(fn) {
        // 创建一个新对象
        var myObj = {};
        // 将this引用该对象，并且讲该对象的继承指向构造函数的原型对象
        myObj.__proto__ = fn.prototype;
        fn.call(myObj);
        // 将该对象返回
        return myObj;
    }
```

## 18、实现深度拷贝
```javascript
/*
* 深度拷贝
*/
function deepCopy (originObject, copyObject) {
    var copyObj = copyObject || {};
    for (var i in originObject) {
        if (typeof originObject[i] === 'object') {
            // 是对象
            if (originObject[i].constructor === Array) {
                // 是数组
                copyObject[i] = [];
            } else {
                copyObject[i] = {};
            }
            return deepCopy(originObject[i], copyObject[i]);
        } else {
            copyObject[i] = originObject[i];
            return copyObject[i];
        }
    }
}
```

## 19、快排
```javascript
function quickSort (arr) {
    if (arr.length < 2) {
        return arr;
    }

    var pivoIndex = Math.floor(arr.length / 2);
    var pivot = arr.splice(pivoIndex, 1)[0];

    var left = [];
    var right = [];

    var length = arr.length;
    for(var i = 0; i < length; i++) {
        if (arr[i] < pivot) {
            left.push(arr[i]);
        } else {
            right.push(arr[i])
        }
    }

    return quickSort(left).concat(pivot, quickSort(right));
}

```

## 20、300ms延时问题
首先是移动端的事件touch:

- touchstart 手指开始触摸屏幕时触发事件
- touchmove 手机在屏幕上移动时，连续触发事件
- touchend 手指离开屏幕时，触发事件
- touchcance 触发事件被意外事件（如电话或者弹窗）终止时触发，使用较少

1. 触摸事件的响应顺序

    - touchstart 
    - touchmove
    - touchend 
    - onclick 300ms 延时

2. 300ms双击缩放
  应对小屏幕浏览桌面端站点的问题，约定了 双击缩放，也就是会有300ms延迟的主要问题，当用户一次点击屏幕之后，浏览器并不能立刻判断用户是否要进行双击缩放，还是要进行单机操作，因此ios的Safari就会等待300ms，以判断用户是否再次点击了屏幕。

  可以用fastClick解决，也可以用zepto的tap事件解决，但是tap事件会有点透问题

  用户点击屏幕的时候会产生两个事件，一个是touch事件，一个是click事件，touch事件会先执行，再执行click事件，（300ms才会触发click）。使用fastClick的原理就是在touchstart阶段，阻止系统的click冒泡，
  dom.addEventListener('touchstart', function (e) {
      e.preventDefault();
  });

  当手指触摸到屏幕的时候，系统生成两个事件，一个是touch 一个是click，touch先执行，touch执行完成后，A从文档树上面消失了，而且由于移动端click还有延迟200-300ms的关系，当系统要触发click的时候，发现在用户点击的位置上面，目前离用户最近的元素是B，所以就直接把click事件作用在B元素上面了.

  dom.addEventListener('touchend', function(e) {
      e.preventDefault();
    });


    
