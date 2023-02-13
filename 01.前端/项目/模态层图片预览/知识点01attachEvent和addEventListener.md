# `attachEvent`和`addEventListener`

`attachEvent`和`addEventListener`在前端开发过程中经常性的使用，他们都可以用来绑定脚本事件，取代在html中写`obj.onclick=method`。

相同点：

　　它们都是dom对象的方法，可以实现一种事件绑定多个事件处理函数。



```js
obj = document.getElementById("testdiv");
obj.onclick=function(){alert('1');};
obj.onclick=function(){alert('2');};
obj.onclick=function(){alert('3');};
// 当使用上边三句话进行事件绑定的时候，很明显当点击ID为testdiv对象时，
// 只能执行最后一句脚本。因为onclick作为一个事件处理对象，只能赋一个值，后面的赋值自动覆盖前面的
```



使用`attachEvent`和`addEventListener`时则可以实现多个事件处理函数的调用

```js
obj = document.getElementById("testdiv");
obj.attachEvent('onclick',function(){{alert('1');});
obj.attachEvent('onclick',function(){{alert('2');});
obj.attachEvent('onclick',function(){{alert('3');});//点击时，三个方法都会执行
```

```js
obj = document.getElementById("testdiv");
obj.addEventListener('click',function(){{alert('1');},false);
obj.addEventListener('click',function(){{alert('2');},false);
obj.addEventListener('click',function(){{alert('3');},false);//点击时，三个方法都会执行
```

不同点：

　　1. `attachEvent`是IE有的方法，它不遵循W3C标准，而其他的主流浏览器如FF等遵循W3C标准的浏览器都使用`addEventListener`，所以实际开发中需分开处理。

　　2. 多次绑定后执行的顺序是不一样的，`attachEvent`是后绑定先执行，`addEventListener`是先绑定先执行。

```js
obj = document.getElementById("testdiv");
obj.attachEvent('onclick',function(){{alert('1');});
obj.attachEvent('onclick',function(){{alert('2');});
obj.attachEvent('onclick',function(){{alert('3');});
//执行顺序是alert(3),alert(2),alert(1);
```

```js
obj = document.getElementById("testdiv");
obj.addEventListener('click',function(){{alert('1');},false);
obj.addEventListener('click',function(){{alert('2');},false);
obj.addEventListener('click',function(){{alert('3');},false);
//点击obj对象时,执行顺序为alert('1'),alert('2'),alert('3');
```

　　3. 绑定事件时，`attachEvent`必须带on，如`onclick`，`onmouseover`等，而`addEventListener`不能带on，如`click`，`mouseover`。这个区别在以上代码中可见。

　　4. `attachEvent`仅需要传递两个参数，而`addEventListener`需要传递三个参数，这里牵扯到“事件流”的概念。侦听器在侦听时有三个阶段：捕获阶段、目标阶段和冒泡阶段。顺序为：捕获阶段（根节点到子节点检查是否调用了监听函数）→目 标阶段（目标本身）→冒泡阶段（目标本身到根节点）。此处的参数确定侦听器是运行于捕获阶段、目标阶段还是冒泡阶段。 如果将 useCapture 设置为 true，则侦听器只在捕获阶段处理事件，而不在目标或冒泡阶段处理事件。 如果useCapture 为 false，则侦听器只在目标或冒泡阶段处理事件。 要在所有三个阶段都侦听事件，请调用两次`addEventListener`，一次将 useCapture 设置为 true，第二次再将 useCapture 设置为 false。 

为了解决浏览器的兼容性可以写如下代码：

```js
function addEvent(elm, evType, fn, useCapture) 
{
    if (elm.addEventListener) 
    {
        // W3C标准
        elm.addEventListener(evType, fn, useCapture);
        return true;
    }
    else if (elm.attachEvent) 
    {
        //IE
        var r = elm.attachEvent(‘on‘ + evType, fn);//IE5+
        return r;
    }
    else 
    {
        elm['on' + evType] = fn;//DOM事件
    }
}
```



```js
        function addEvent(element, type, handler) {
            //为每一个事件处理函数分派一个唯一的ID
            if (!handler.$$guid) handler.$$guid = addEvent.guid++;
            //为元素的事件类型创建一个哈希表
            if (!element.events) element.events = {};
            //为每一个"元素/事件"对创建一个事件处理程序的哈希表
            var handlers = element.events[type];
            if (!handlers) {
                handlers = element.events[type] = {};
                //存储存在的事件处理函数(如果有)
                if (element["on" + type]) {
                    handlers[0] = element["on" + type];
                }
            }
            //将事件处理函数存入哈希表
            handlers[handler.$$guid] = handler;
            //指派一个全局的事件处理函数来做所有的工作
            element["on" + type] = handleEvent;
        };
        //用来创建唯一的ID的计数器
        addEvent.guid = 1;
        function removeEvent(element, type, handler) {
            //从哈希表中删除事件处理函数
            if (element.events && element.events[type]) {
                delete element.events[type][handler.$$guid];
            }
        };
        function handleEvent(event) {
            var returnValue = true;
            //抓获事件对象(IE使用全局事件对象)
            event = event || fixEvent(window.event);
            //取得事件处理函数的哈希表的引用
            var handlers = this.events[event.type];
            //执行每一个处理函数
            for (var i in handlers) {
                this.$$handleEvent = handlers[i];
                if (this.$$handleEvent(event) === false) {
                    returnValue = false;
                }
            }
            return returnValue;
        };
        //为IE的事件对象添加一些“缺失的”函数
        function fixEvent(event) {
            //添加标准的W3C方法
            event.preventDefault = fixEvent.preventDefault;
            event.stopPropagation = fixEvent.stopPropagation;
            return event;
        };
        fixEvent.preventDefault = function () {
            this.returnValue = false;
        };
        fixEvent.stopPropagation = function () {
            this.cancelBubble = true;
        };
```



```js
var addEvent = (function () {
            if (document.addEventListener) {
                return function (el, type, fn) {
                    if (el.length) {
                        for (var i = 0; i && el.length; i++) {
                            addEvent(el[i], type, fn);
                        }
                    } else {
                        el.addEventListener(type, fn, false);
                    }
                };
            } else {
                return function (el, type, fn) {
                    if (el.length) {
                        for (var i = 0; i && el.length; i++) {
                            addEvent(el[i], type, fn);
                        }
                    } else {
                        el.attachEvent('on' + type, function () {
                            return fn.call(el, window.event);
                        });
                    }
                };
            }
        })();
```