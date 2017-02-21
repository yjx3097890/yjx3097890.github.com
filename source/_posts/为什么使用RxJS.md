---
title: 为什么使用RxJS?
cover: //reactivex.io/assets/reactivex_bg.jpg
comments: true
date: 2017-02-22 02:06:04
tags: [RxJS, rx-book]
categories: [计算机技术]
---
原文链接 http://xgrommx.github.io/rx-book/why_rx.html
<!-- more --> 

你可能会问，为什么用RxJS？Promises怎么样？Promises可以很好的解决异步操作如对服务器进行Ajax请求，它期望的行为是一个值然后请求完成。 Reactive Extensions for JavaScript统一了Promises, 回调函数, （DOM输入）事件数据, Web Workers, Web Sockets。一旦我们统一了这些概念，就可以开发复杂的应用。
为了让你了解复杂的应用，我们建一个自动补齐服务。它会提取用户在文本框里的输入，然后查询服务器，要确保不要让服务器被每个按键发起的请求所淹没，而是以一个更自然的速度发起请求。
首先，引用js文件，包括jQuery，虽然Rxjs不依赖于jQuery：

    <script src="http://code.jquery.com/jquery.js"></script>
    <script src="rx.lite.js"></script>

然后，从input中获得用户输入，通过Rx.Observable.fromEvent 监听keyup事件。这可以使用jQuery, Zepto, AngularJS and Ember.js 的事件绑定，或者回退到使用原生的事件绑定。RxJS使你以一致的方式根据你的框架处理事件，这没什么惊奇。

#### part1.js

    var $input = $('#input'),
        $results = $('#results');
    
    /* Only get the value from each key up */
    var keyups = Rx.Observable.fromEvent($input, 'keyup')
        .map(e => e.target.value)
        .filter(text => text.length > 2);
    
    /* Now throttle/debounce the input for 500ms */
    var throttled = keyups.throttle(500 /* ms */);
    
    /* Now get only distinct values, so we eliminate the arrows and other control characters */
    var distinct = throttled.distinctUntilChanged();

现在查询Wikipedia！在RxJS中，我们可以通过Rx.Observable.fromPromise 绑定任何Promises A+的实现，或直接返回promise，我们替你处理它。

    function searchWikipedia (term) {
        return $.ajax({
            url: 'http://en.wikipedia.org/w/api.php',
            dataType: 'jsonp',
            data: {
                action: 'opensearch',
                format: 'json',
                search: term
            }
        }).promise();
    }

一旦这个被建立，我们就可以将输入节流和查询服务组合起来。这里我们使用flatMapLatest获得返回值，确保不会打乱请求队列顺序。

    var suggestions = distinct.flatMapLatest(searchWikipedia);

最后我们调用observable队列的subscribe方法拉取data。

    suggestions.subscribe(data => {
        var res = data[1];
    
        /* Do something with the data like binding */
        $results.empty();
    
        $.each(res, (_, value) => $('<li>' + value + '</li>').appendTo($results));
    }, error => {
        /* handle any errors */
        $results.empty();
    
        $('<li>Error: ' + error + '</li>').appendTo($results);
    });

