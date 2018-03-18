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