# 整理前端面试题
---

### 1、盒模型
```
    盒模型有标准W3C盒模型和怪异盒模型两种
    
    根据box-sizing的值可以来设置是 W3C盒模型 还是 怪异盒模型

    属性为 content-box 为W3C 盒模型 它的内容为 content + padding + border + margin
          border-box 为怪异盒模型 它的内容为 content + margin (它的content 包括 content + padding + border)
```

### 2、3栏布局
```
    1、最好的方法  直接flex

    2、自适应部分们可以用calc 计算宽度，左右两个float正常布局就行

    3、注意一下双飞翼布局，中间的div再包裹一个子div  三个外部的div都用float布局
```

### 3、== 与 === 的区别
``` 
    === 直接判断类型，在判断是否相等

    == 会有隐式类型转换

    string/number/boolean/object  ---> (false) undefined/null

    string ---> number  string 要先转换成number

    boolean ---> number boolean 要先转换成number

    object ---> 先通过valueOf进行转换，如果为基本类型值则进行比较，否则再进行toString转换，如果还不相等，则进行number转换后比较

```

### 4、typeof的各种
```
    注意一下 function 类型

    typeof null === 'object';
    typeof NaN === 'number'
    typeof undefined === 'undfined'
```

### 5、instanceof 的 原理
```
        A instanceof B 的原理是
        查看对象B的prototype 属性指向的原型对象是否在对象A的原型链上，若在则返回true，若不在则返回false
        简而言之

        就是 

        A.__proto__.__proto__.... = B.prototype;

        任何实例对象的__proto__ 都是指向其构造函数的prototype  记住这句话，很重要！！！！
        __proto__ 隐式原型，作用是形成原型链
        prototype 显示原型，生成原型对象，方便所有实例，共享原型对象上的方法和属性
```

### 6、实现bind(使用apply、call)
```
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

### 7、实现apply
```
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

### 8、实现一个弹窗，使用单例模式
```
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


    该题的关键点在于，用立即执行函数生成一个实例，保证一个构造函数只有一个实例
```

### 9、观察者模式
```
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

### 10、继承
```
继承有四种
1、原型继承 
 - 原型继承的缺点，子构造函数不能像父构造函数传递参数，原型上的引用类型值会被所有的实例所共享

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

2、构造函数继承 
 - 所有的实例都会生成一个自己的副本，解决了原型上的引用类型被共享的问题，但是同时构造函数中的函数却因为每个实例都生成了一个副本导致不能复用，这是构造函数的缺点。

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

3、组合继承
 - 组合继承很好的解决了引用类型的值放在实例中，而需要被共享的函数放在原型上，组合继承的缺点在于，子构造函数需要调用两次父构造函数

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

4、继承式继承
 - 利用Object.create 很好的解决了，调用两次父构造函数的问题
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