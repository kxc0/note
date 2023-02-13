# event.preventDefault

[`Event`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event) 接口的 **`preventDefault()`** 方法，告诉[用户代理](https://developer.mozilla.org/zh-CN/docs/Glossary/User_agent)：如果此事件没有被显式处理，它默认的动作也不应该照常执行。此事件还是继续传播，除非碰到事件监听器调用 [`stopPropagation()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopPropagation) 或 [`stopImmediatePropagation()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/stopImmediatePropagation)，才停止传播。

### [语法](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault#语法)

```
event.preventDefault();
```

Copy to Clipboard

### [参数](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault#参数)

无

### [返回值](https://developer.mozilla.org/zh-CN/docs/Web/API/Event/preventDefault#返回值)

```
undefined
```