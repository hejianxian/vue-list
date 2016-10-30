# vue-list

> A solution to build an infinite load more list component.

[在线DEMO](https://hejx.herokuapp.com/vue-list/)

## 序

众所周知，列表滚动加载这种需求很常见，特别是在移动webapp上。Github上各款移动端ui框架都会包含滚动加载功能的组件，但它们往往只提供很基础的功能，例如简单地监听滚动条并在满足条件的时候触发回调，然后通过某些方法把新的元素加入到页面末尾。这样的确可以解决分页加载数据的问题，但是在一个数据量比较大的情况下，页面元素会增加得很快，这时就会导致性能问题，想象一下，如果一个移动端页面上有1万条数据需要显示在页面的时候，是多么恐怖的事情。

然后，这个repo并不是要提供一个完整的滚动加载组件，而是，提供一种在数据量大的情况下，对列表的滚动加载进行优化的解决方案。

> 我相信有不少人知道方法，但也应该有不少人还不了解。

## 先来一张图
![view]()


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
```
