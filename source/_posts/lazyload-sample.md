---
title: 图片懒加载（lazyload）的简单实现
categories: FE
tags: lazyLoad
abbrlink: 4bdcf2eb
date: 2019-07-12 16:14:38
---


针对图片类网站或展示图片过多的部分页面，为减轻服务器压力、提高加载速度、优化用户体验，我们需对暂未出现在可视区域内的图片先不做加载，之后侦听滚动事件，待图片滚至可视区域再加载。

## 原理

将img的src指向默认loading（推荐）或干脆不设置，同时令`data-src`（命名可自定义）指向实际src，从而实现动态加载图片的机制。
<!-- more -->

### 视口的宽高与滚动高度
在写代码之前，先对几个容易混淆的知识点稍作介绍：

![viewport.png](https://i.loli.net/2018/05/19/5aff07e271200.png)

1. 屏幕视口大小（图中1、2）
    - 原生：
        `window.innerHeight`    标准浏览器及IE9+
        `document.documentElement.clientHeight`     标准浏览器及低版本IE标准模式
        `document.body.clientHeight`    低版本混杂模式
        PS：事实上后两种方式获取的高度与`window.innerHeight`有所不同，这3个属性的值逐个减小：
        `window.innerHeight`    包括整个DOM：内容、边框以及滚动条。
        `documentElement.clientHeight`    不包括整个文档的滚动条，但包括 html 的边框。
        `body.clientHeight`     除不包括 html 边框外，还不包括 body 的边框和滚动条。
    - jQuery： 
        `$(window).height();`


2. 滚动条滚动距离（图中3、4）
    - 原生：
        `window.pageYOffset`    IE9+及标准浏览器
        `document.documentElement.scrollTop`    兼容ie低版本的标准模式
        `document.body.scrollTop`    兼容混杂模式
    - jQuery：
        `$(document).scrollTop();`

## 代码

### HTML
```
<img class="lazyload" data-src=url">
```

### CSS

略过，注意图片需设置宽高。

### JS
```javascript
(function() { //立即执行函数
        let imgList = [], // 页面所有img元素数组
            delay, // setTimeout 对象
            time = 250, // 延迟载入时间
            offset = 0, // 设置图片距离视口多远则立即加载的偏移量
            _selector; // 默认为 `.lazyload`

        function _isShow(el) { //判断img是否已进入视口
            let coords = el.getBoundingClientRect();
            return ((coords.top >= 0 && coords.left >= 0 && coords.top) <= (window.innerHeight || document.documentElement.clientHeight) + parseInt(offset));
        }

        function _load() { //img加载
            for (let i = 0, len = imgList.length; i < len; i++) {
                if (_isShow(imgList[i])) {
                    imgList[i].src = imgList[i].getAttribute('data-src');
                    imgList.splice(i, 1);
                }
            }
        }

        function _delay() { //函数节流
            clearTimeout(delay);
            delay = setTimeout(() => {
                _load();
            }, time);
        }

        function lazyLoad(selector) {
            _selector = selector || '.lazyload';
            let nodes = document.querySelectorAll(selector);
            imgList = Array.apply(null, nodes);
            _delay(); //避免首次加载未触发scroll事件,主动触发一次加载函数
            window.addEventListener('scroll', _delay, false);
        }

        lazyLoad('.lazyload');

})();
```

### 步骤说明

1. 判断图片是否进入视口_isShow

    通过调用`getBoundingClientRect()`方法，获得一组包含元素用于描述其边框相对于视口左部及顶部位置的只读属性——left、top、right与bottom，再与浏览器视口宽高对比即可判断元素是否已进入视口，offset为自定义偏差值，按实际需求确定。

    `top`：元素上边框与浏览器视口顶部间距
    `left`：元素左边框与浏览器视口左侧间距
    `right`：元素右边框与浏览器视口左侧间距
    `bottom`：元素底边框与浏览器视口顶部间距

2. 图片加载_load

    循环输出每个imgList中保存的图片对象，由`_isShow`函数判断是否需要加载图片，需要则立即从img的`data-src`属性中取得链接并赋值给src.完成上述动作后，`splice()`方法从数组中剔除该img对象引用，避免下次循环重复判断。

3. 声明节流函数_delay

    在类似`scroll`、`resize`事件中执行大量DOM操作或计算时，函数会被高频触发，导致性能下降，影响用户体验。 
    原理：每次执行`_delay`函数前先清除上一次`setTimeout`生成的引用，阻止上一次的函数调用（如果尚未执行），然后创建一个新的setTimeout，在time保存的时间间隔后调用函数。

4. 侦听scroll事件，执行节流函数

    使用`document.querySelectorAll()`方法获取需要实现懒加载的图片列表。 
    注意这里得到的只是一个nodeList，需要用`Array.apply`将nodes转变成一个数组保存至imgList中。 



至此，图片懒加载功能初步完成。
