# 原生 JS实现一个简单分页插件

---

## 简单介绍

先上效果图：

![pagination](./img/pagination.gif)

有点类似于 `Github`搜索结果页的底部分页，可能部分逻辑有点出入，但总体应该差不多

代码使用 `ES6`语法，这个插件其实是一个 `class`，`babel`打包后可兼容到 `IE9`，原生js无任何依赖。

主要的判断逻辑都在 `gotoPage`这个方法中，因为需要考虑到各种情况，所以里面写了很多的 `if.. else...`判断，没写下这些代码之前，我觉得这个东西充其量也就几个判断差不多了，但是没想到会有这么多，每次的页码切换都对应着不同的判断分支，比较繁琐。

---
## 核心代码简要分析

核心方法是 `gotoPage`，乍一看，此方法中到处都是 `if...else..`判断，似乎无从下手，但是不论页码如何变，也不管是如何切换到某个页码的，只需要记住一点，那就是切换到某个页码时，这个页码对应的整个分页组件的状态是**确定**的，这一点很重要，只要把握住了这点，那一连串的 `if...else...`判断的意义其实也就清晰了。

此方法最外层的一个 `if...else...`判断是针对页码是否需要显示省略号占位符的，如果页码总数小于页面上课同时存在的页码数，那么就不需要省略符号占位了，就是一种比较简单的分页，例如下面这种：

![simple](./img/1.png)

对于这种情况，只需要在切换页码的时候，变换 `active`的页码即可。

至于另外那种需要省略号占位的情况，才是复杂的地方，而这个分支里，最外层有 `3`个判断，这 `3`个判断包括了所有页码切换的情况。

第一个判断是针对分页组件左边不需要出现省略号占位符的情况，例如下面这种：

![2](./img/2.png)

第二个判断是针对分页组件右边不需要出现省略号占位符的情况，例如下面这种：

![3](./img/3.png)

第三个判断是针对分页组件两边都需要出现省略号占位符的情况，例如下面这种：

![4](./img/4.png)

整个分页组件的所有状态，肯定都被包括在这三种状态中，所以接下来的逻辑判断只需要针对这三个状态就行了。

---

## 用法

首先 `new`这个类，然后调用 `init`方法，传入相应的参数即可，例如：
```js
const slp = new SimplePagination(12)
slp.init({
  container: '.box',
  maxShowBtnCount: 3,
  onPageChange: state => {console.log('pagination change:', state.pageNumber)}
})
```
其中，在 `new`实例化 `SimplePagination`类的时候，需要传入 `1`个参数，即总页数(`totalPageCount`)，分页插件需要根据此值来进行页码元素的绘制。

调用 `init`方法时，为了方便传参，此方法接收一个对象，对象中存在以下属性：

|参数名|类型|`default`|说明|是否必填|
|---|---|---|---|---|
|`container`|`string`|`body`|一个`DOM`元素的选择器，`id` 或者 `class`均可，表示分页的容器|否|
|`maxShowBtnCount`|`number`|`5`|不包括开头和结尾的两个固定按钮外，中间最多展示几个数字页码按钮|否|
|`pCName`|`string`|`page-li`|所有的分页页码元素的统一类名，包括上一页、下一页|否|
|`activeCName`|`string`|`page-active`|当选中页码时添加的类名`class`|否|
|`dataNumberAttr`|`string`|页码元素上的一个属性，其值为页码元素所表示的页码|`data-number`|否|
|`prevCName`|`string`|`page-prev`|上一页 按钮的类名`class`|否|
|`nextCName`|`string`|`page-next`|下一页 按钮的类名`class`|否|
|`disbalePrevCName`|`string`|`no-prev`|禁用 上一页 按钮的可用性时给此按钮添加的类名`class`|否|
|`disbaleNextCName`|`string`|`no-next`|禁用 下一页 按钮的可用性时给此按钮添加的类名`class`|否|
|`pageNumberCName`|`string`|`page-number`|不包括 上一页 下一页、省略号占位符按钮之外的所有页码元素统一类名|否|
|`swEvent`|`string`|`click`|触发切换页面的事件|否|
|`onPageChange`|`string`|-|页码切换时的回调函数|否|

除了根据页面上的页码和上一页、下一页按钮进行页码切换外，插件还有一个 `gotoPage`方法可用，此方法接收一个类型为 `number`的参数，调用此方法，就会跳到此参数指定的页码上，例如，跳转到页码 `9`上：
```js
slp.gotoPage(9)
```

如果传入的参数不是合法的页码，则不会进行任何操作。

>`SimplePagination`这个类主要在于页码切换的逻辑判断，外加简单地组装了 `DOM`结构，默认是没有样式的，我在示例代码中随便写了个样式，如果你不喜欢这个样式完全可以自己重写。