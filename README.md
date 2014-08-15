infiniteScrollPage
======

[先点击查看demo](http://zhangchen2397.github.io/infiniteScrollPage/demo/) (建议扫描下方二维码手机上体验效果更佳)

![qr code](http://zhangchen2397.github.io/infiniteScrollPage/demo/images/qrcode.png)

###组件简介
在移动webapp开发中，常见的列表布局展示，和PC传统列表有些区别的是，移动端这种列表的分页方式一般为下拉自动分页或点击加载更多按钮分页，针对这种常见的使用场景，无限下拉分页组件抽离出这种逻辑，可以方便的配置下拉自动分页及最大自动分页数、手动加载分页等。

###组件主要功能
1. 自定义距离页面底部多少时预加载下一页内容
2. 可配置最大自动加载页数，当当前页大于最大自动加载页数时出现手动加载更多按钮
3. 请求数据失败时自动尝试重连接，当重连3次依然失败时派发加载失败事件
4. 可方便自定义自动加载及手动加载样式
5. 加载成功或失败派发相应事件，相互解耦，灵活调用

###配置参数说明
```javascript
this.defaultConfig = {
    /**
     * el {string|jq object} 外层容器，可为字符串或jq对象
     * conListWrap {string} 内容列表元素class
     * autoLoading {string} 自动加载元素class
     * clickLoading {string} 手动点击加载元素class
     */
    el: '#container',
    conListWrap: '.con-list-wrap',
    autoLoading: '.auto-loading',
    clickLoading: '.click-loading',

    /**
     * ajaxUrl {string} 请求URL，不需要带分页参数
     * pagePara {string} 分页参数名称，具体页数内部自动处理
     */
    ajaxUrl: './api.json',
    pagePara: 'pn',

    /**
     * maxAutoPage {number} 最大自动加载页数
     *  - 当当前页数超过maxAutoPage时，显示手动加载更多按钮
     *  - 当maxAutoPage为0时，不自动加载分页，显示手动加载更多
     * offsetHeight {number} 自动加载时机，离页底像素距离
     */
    maxAutoPage: 5,
    offsetHeight: 60,

    /**
     * ajax请求参数配置，其它参数按默认值
     * timeout {number}  请求超时时间
     * dataType {string} 请求类型
     *  - 默认值为text/html，为普通的ajax请求，后端返回json格式数据
     *  - 当dataType为jsonp时，为jsonp请求
     */
    timeout: 5000,
    dataType: 'text/html',

    /**
     * dataType {object} 指定将自定义scroll事件绑定在当前事件上
     * 如curMod不为空，则scroll事件绑定在全局scroll事件上
     */
    curMod: null
};
```

###对外调用接口及自定义事件
```javascript
 /**
 * 对外调用接口及自定义事件
 * @method ajaxData 请求数据接口
 * @customEvent ajaxSuccess 请求数据成功时派发事件
 * @customEvent ajasError 请求数据失败时派发事件
 * @customEvent ajasFinalError 尝试3次重新请求后依然失败事件
 *
 */
 ```

###基本html结构
```html
<div id="container">
    <div class="con-list-wrap"></div>
    <div class="auto-loading">加载中...</div>
    <div class="click-loading">点击加载更多</div>
</div>
```
###组件初始化及使用
```javascript
( function() {
    var Isp = infiniteScrollPage;

    //初始化组件
    infiniteScrollIns = Isp.createInfinteScrollPage( {
        ajaxUrl: './api.html',
        maxAutoPage: 5
    } );

    //监听数据请求成功事件，用于拼接模板
    $.bind( infiniteScrollIns, 'ajaxSuccess', function( event ) {
        this.conListWrap.append( event.data );
    } );

    //监听数据请求失败事件
    $.bind( infiniteScrollIns, 'ajaxError', function() {
        //可用于打错误日志统计
    } );

    //监听重试3次后依然数据请求失败事件，用于给出错误信息提示
    $.bind( infiniteScrollIns, 'ajaxFinalError', function() {
        //可用于打错误日志统计或错误提示
        alert( '加载数据失败，请重试' );
    } );
} )();
```