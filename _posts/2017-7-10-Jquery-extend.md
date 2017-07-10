---
layout: post
title: jQuery插件开发
description: "此插件非彼插件"
tags: [jQuery]
modified: 2017-07-10
image:
  feature: abstract-2.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---

在实际开发工作中，总会碰到像滚动，分页，日历等展示效果的业务需求，对于接触过jQuery以及熟悉jQuery使用的人来说，首先想到的肯定是寻找现有的jQuery插件来满足相应的展示需求。目前页面中常用的一些组件，都有多种jQuery插件可供选择，网络上也有很多专门收集jQuery插件的网站。

### 什么是jQuery插件？

jQuery插件开发就是扩展jQuery原型对象的方法，简单来说就是jQuery插件是jQuery对象的方法。

1. 为什么是扩展jQuery原型对象而不是jQuery全局对象呢？

因为我们知道jQuery的大部分内容都是围绕DOM操作，所以插件开发当然也是围绕jQuery对象的DOM操作。

2. jQuery原型对象是什么？jQuery插件开发是如何扩展jQuery原型对象的？

- 我们知道每个jQuery对象都会包含jQuery原型对象中定义的DOM操作方法。
- 那么jQuery对象是如何获取这些方法的呢？其实jQuery内部定义了一个jQuery.fn对象，查看jQuery源码可以发现jQuery.fn=jQuery.prototype，也就是说jQuery.fn对象是jQuery的原型对象，jQuery对象就可以通过原型继承这些方法。
- jQuery的DOM操作方法都是在jQuery.fn对象上定义的，那么，所谓的jQuery插件开发即是扩展jQuery.fn对象的方法。

### jQuery插件开发

假如我们现在需要一个jQuery插件用来改变标签内容颜色

1. 基础版jQuery插件

```javascript
$.fn.changeStyle = function(colorStr){
         this.css("color",colorStr);
}
//调用
$("p").changeStyle("red");
```

2. 满足链式调用的jQuery插件

```javascript
$.fn.changeStyle = function(colorStr){
         this.css("color",colorStr);
         return this;
}
```

3. 防止$符号污染的jQuery插件

```javascript
(function($){
     $.fn.changeStyle = function(colorStr){
         this.css("color",colorStr);        
         return this;
     }
}(jQuery));
```

4. 可以接受参数的jQuery插件

```javascript
(function($){
     $.fn.changeStyle = function(colorStr，fontSize){
         this.css("color",colorStr).css("fontSize",fontSize+"px");        
         return this;
     }
}(jQuery));
```

参数比较多的时候，我们可以定义一个对象用来存放参数

```javascript
(function($){
     $.fn.changeStyle = function(option){
         this.css("color",option.colorStr).css("fontSize",option.fontSize+"px");        
         return this;
     }
}(jQuery));
//调用
$("p").changeStyle({colorStr:"red",fontSize:14});
```

把参数放到一个对象中传给插件还有一个好处就是我们可以在插件内部为一些参数定义一些缺省值。

```javascript
(function($){
     $.fn.changeStyle = function(option){
          //存储缺省值的对象
          var defaultSetting = {colorStr:"green",fontSize:12};
          //使用jQuery的extend方法,将参数对象合并到存储缺省值的对象
          var setting = $.extend(defaultSetting,option);
          this.css("color",setting.colorStr).css("fontSize",setting.fontSize+"px");        
         return this;
     }
}(jQuery));
```

5. 最终版

当开发的插件较多的时候，我们也可以使用jQuery的extend方法。

```javascript
(function($){
     $.fn.extend({         
         changeStyle:function(option){             
         var defaultSetting = { colorStr:"green",fontSize:12};
         var setting = $.extend(defaultSetting,option);
         this.css("color",setting.colorStr).css("fontSize",setting.fontSize+"px");        
         return this; 
          }
     });
}(jQuery));
```

### jQuery的extend方法

这是我们在插件开发中经常使用的方法，所以简单介绍下。

1. 方法原型：extend(dest,src1,src2,src3...);

它的含义是将src1,src2,src3...合并到dest中，返回值为合并后的dest。如下：

```javascript
var result = {};
$.extend(result,{name:"Tom",age:21},{name:"Jerry",sex:"Boy"})
console.log(result);  //Object {name: "Jerry", age: 21, sex: "Boy"}
```

也就是说后面的参数如果和前面的参数存在相同的名称，那么后面的会覆盖前面的参数值。

2. 省略dest参数

上述的extend方法原型中的dest参数是可以省略的，如果省略了，则该方法就只能有一个src参数，而且是将该src合并到调用extend方法的对象中去，如：

- $.extend(src)   该方法将src合并到jQuery的全局对象中去，可以用来扩展jQuery的公共方法。
- $.fn.extend(src)    该方法将src合并到jQuery的原型对象中去，即先前我们用来定义插件。

此文参考：

[jQuery.extend 函数使用详解](http://www.cnblogs.com/zikai/p/5074686.html)
[掌握jQuery插件开发](http://www.jianshu.com/p/518d424d4994)