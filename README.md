<p align="center">
  <a href="https://hejx.herokuapp.com/vue-list/">
    <img src="https://github.com/Alex-fun/vue-list/blob/master/src/assets/preview.2.png?raw=true" width="500" center alt="view"/>
  </a>
</p>
<p align="center">
  <a href="https://hejx.herokuapp.com/vue-list/">
    在线DEMO
  </a>
</p>

## 序

众所周知，列表滚动加载这种需求很常见，特别是在移动webapp上。Github上各款移动端ui框架都会包含滚动加载功能的组件，但它们往往只提供很基础的功能，例如简单地监听滚动条并在满足条件的时候触发回调，然后通过某些方法把新的元素加入到页面末尾。这样的确可以解决分页加载数据的问题，但是在一个数据量比较大的情况下，页面元素会增加得很快，这时就会导致性能问题，想象一下，如果一个移动端页面上有1万条数据需要显示在页面的时候，是多么恐怖的事情。

然后，这个repo并不是要提供一个完整的滚动加载组件，而是，提供一种在数据量大的情况下，对列表的滚动加载进行优化的解决方案。

ps: 我相信有不少人知道方法，但也应该有不少人还不了解。


## 思考

首先，为何要对数据量大的列表页进行滚动加载优化呢？主要的一个原因就是页面上元素太多了，滚动的时候会有卡顿的问题，移动端上更为明显。

那既然元素太多导致的问题，解决方法不就很明显了吗？没错，就是 **减少页面元素**

OK，假设现在列表的元素结构是酱紫的：

```html
<ul class="list">
  <li class="item">...</li>
  ...
</ul>
```

那么现在有几个问题要解决的：

* 什么时候开始删除元素，什么时候把删了的元素显示回来
* 被删了的元素会导致高度减少，怎样保持总高度不变
* 如何确保列表元素显示在应该出现的位置
* 什么时候开始加载新数据


## 先来一张图

<img src="https://github.com/Alex-fun/vue-list/blob/master/src/assets/view.png?raw=true" width="250" alt="view"/>

那么在开始之前，必须要先说明白上图的3色块的作用。

首先，这里有一个前提，就是**列表的每个item的高度要一致**，这主要是为了方便计算。

* above: 当前显示列表的上方，一般高度为screen高度的2倍
* screen: 我们可以理解为屏幕或者是显示列表的容器，我们主要是需要通过它来计算出当前screen里可以显示多少个item
* below: 当前屏幕下方

默认情况下，它们所占的比例为2:1:1，那么这里要区分这3个区域，主要是为了列表在滚动的时候能够确保列表中有内容显示。这里最好就是直接看demo，更容易理解。

## 制作

#### 一、保持总高度不变

我们先把列表的元素结构改一下：

```html
<div class="content">
  <ul class="list">
    <li class="item">...</li>
    ...
  </ul>
</div>
```

为啥要改成这样呢？首先，我们是要删除元素的，为了保持总高度不变，我们要把ul这个元素的高度设为所有item加起来的总高度，那此时列表的screen就是类名为content的div元素了，也就是说整个ul列表是在叫content的这个div里滚动。

那么这里还是那个问题，怎样保持ul的总高度不变，其实方法有很多，

* 1: 对ul设置总高度，然后显示item进行相对定位
* 2: 在列表的开始和结尾各放一个元素来撑开高度
* 3: 和方法1差不多，使用padding或者margin来撑开顶部高度（本列使用的方法）

#### 二、删除和显示元素

基于上一步，为了方便计算，我们只以顶部为计算目标，这样的一个好处就是，我们不需要管底部是什么情况，一切以顶部为准，进行元素的删除和显示。

```js
if (this.lastScrollTop === null || Math.abs(_scrollTop - this.lastScrollTop) > this._max) {
    this.lastScrollTop = _scrollTop;
} else {
    return;
}
```

根据我们之前定义的几个色块，这里主要是判断当screen（div.content）的scrollTop减去上一次的ScrollTop会大于最大高度_max（_max = screen的items数 * item高度）的时候,就会开始重组列表数据。也就是说，无论是向下还是向上滚动，只要满足`Math.abs(_scrollTop - this.lastScrollTop) > this._max`,都要重组列表数据。那么这里就会引申出下一个问题，应该取数据的那一部分为新的列表数据。

#### 三、生成新数据

基于item的高度是不变的这个条件，其实是很容易可以计算出当前列表的上方有多少个item，根据上面图片的3个色块，在页面上的元素应该要有4 * screen的items数，也就是`above+below+rowsInWindow`。

那么可以通过div.content的scrollTop来计算出应该从列表数据第几个开始截取，也就是超出了above高度的item数，然后它的长度就是`above+below+rowsInWindow`。计算方式大概如下：

```js
let _from = parseInt(_scrollTop / this.height) - this._above;
if (_from < 0) {
    _from = 0;
}
let _to = _from + this._above + this._below + this._rowsInWindow;
if (_to > this.list.length) {
    _to = this.list.length;
}
```

计算出了from和to后，就可以获取新的列表数据了。

```js
this.previewList = [];
for (; from < to; from++) {
    this.previewList.push(this.list[from])
}
```

那么这里，我特意用了一个独立的数组previewList来保存要显示的列表数据，这样做主要是为了保证总列表数据的不变。新的数据生成之后，剩下的事情就交给vue去做了。

#### 四、加载新的数据

这里其实就是最简单的一步了。当列表被拉到最底下的时候，总的列表数据其实已经没有了。那么这个时候有2种情况下应该触发加载新的数据的方法。第一个就是to已经是数据长度的最大值，和在第二步的判断里retrun之前并且页面已经滚到了最底下。

然后在这个时机触发加载更多数据的方法，那么加载了新的数据，form和to必须要重新计算，因为此时的below已经没有任何元素了。重置了from和to之后，再生成新的previewList列表，然后就完成了。

```js
loadmore(from, to) {
  ...
  // fetch mock
  setTimeout(() => {
    for(var i = 0; i < 200; i++) {
      this.list.push({
        title: 'item ' + COUNT++
      });
    }
    let _from = from, _to = to + this._below; // 重新计算，这里还要处理加载回来的数据比below要求的还少的情况
    this.resetPreviewList(_from, _to); // 重新计算previewList
    ...
  }, 2000)
}
```

## 其他

那么，大致上功能算是完成了，然后在demo上，我特意地添加了别的一些数据，这些东西其实在一些后台管理系统的数据table上非常有用。

然后，demo里是每一次会加载200个数据，其实这里还可以再优化一下，可以在上啦的时候，每滚动一个屏幕的item数时，强行出现loading元素，这样在每个item内部元素特别多、条件判断特别多的时候，效果非常明显。

## 最后

这个repo真的完完全全只是提供一种解决方法，也没有经过任何浏览器的兼容测试。假若这个repo对你的项目或者你的学习能有一点点帮助，那也就棒棒哒了。

最后，如果你觉得这个repo还OK的话，可以把它分享给更多的人，或者点一下star！谢谢！

ps: 如果你有更好的想法或者意见，可以提个issue，咱们讨论讨论。

## License

MIT
