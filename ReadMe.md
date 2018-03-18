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

### == 与 === 的区别
``` 
    === 直接判断类型，在判断是否相等

    == 会有隐式类型转换

    string/number/boolean/object  ---> (false) undefined/null

    string ---> number  string 要先转换成number

    boolean ---> number boolean 要先转换成number

    object ---> 先通过valueOf进行转换，如果为基本类型值则进行比较，否则再进行toString转换，如果还不相等，则进行number转换后比较

```