# vue-list

> A solution to build an infinite load more list component.

[在线DEMO](https://hejx.herokuapp.com/vue-list/)

## 序

众所周知，列表滚动加载这种需求很常见，特别是在移动webapp上。Github上各款移动端ui框架都会包含滚动加载功能的组件，但它们往往只提供很基础的功能，例如简单地监听滚动条并在满足条件的时候触发回调，然后通过某些方法把新的元素加入到页面末尾。这样的确可以解决分页加载数据的问题，但是在一个数据量比较大的情况下，页面元素会增加得很快，这时就会导致性能问题，想象一下，如果一个移动端页面上有1万条数据需要显示在页面的时候，是多么恐怖的事情。

然后，这个repo并不是要提供一个完整的滚动加载组件，而是，提供一种在数据量大的情况下，对列表的滚动加载进行优化的解决方案。

ps: 我相信有不少人知道方法，但也应该有不少人还不了解。


## 思考

首先，为何要对数据量大的列表页进行滚动加载优化呢？主要的一个原因就是页面上元素太多了，滚动的时候会有卡顿的问，移动端上更为明显。

那既然元素太多导致的问题，解决方法不就很明显了吗？没错，就是 **减少页面元素**

OK，假设现在列表的元素结构是酱紫的：

```html
<ul>
  <li>...</li>
  ...
</ul>
```

那么现在有几个问题要解决的：

* 什么时候开始删除元素，什么时候把删了的元素显示回来
* 被删了的元素会导致高度减少，怎样保持总高度不变
* 什么时候开始加载新数据


## 先来一张图

<img src="https://github.com/Alex-fun/vue-list/blob/master/src/assets/view.png?raw=true" width="250" alt="view"/>

上图的3个色块分别代表：

* above: 当前屏幕上方，一般高度为screen高度的2倍
* screen: 毫无疑问，当前屏幕
* below: 当前屏幕下方

默认情况下，它们所占的比例为2:1:1

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```
