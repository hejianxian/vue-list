<template lang="html">
  <div class="vue-list" @scroll="handleScroll">
    <ul>
      <li class="line-top" :style="{height: lineTopHeight +'px'}"></li>
      <li v-for="item in previewList">{{item.title}}</li>
      <li class="line-bottom" :style="{height: lineBottomHeight +'px'}"></li>
    </ul>
    <div class="load-more-gif">loading...</div>
  </div>
</template>

<script>

export default {
  name: 'vue-list',
  props: {
    list: {
      type: Array,
      required: true,
      default: [],
      twoWays: true
    },
    height: {
      type: Number,
      default: 44
    },
    canScroll: {
      type: Boolean,
      default: true
    },
    dispatchData: {
      type: Function
    }
  },
  data() {
    return {
      lastScrollTop: null,
      distance: 44,
      lineTopHeight: 0,
      lineBottomHeight: 0,
      canLoadmore: true,
      previewList: [],
      displayCount: 0
    }
  },
  computed: {},
  created() {},
  mounted () {
    this.initData();
    this.handleScroll();
  },
  attached () {},
  methods: {
    initData() {
      this._rowsInWindow = Math.ceil(this.$el.offsetHeight / this.height),
      this._above = this._rowsInWindow * 2,
      this._below = this._rowsInWindow,
      this._max = this._rowsInWindow * this.height;
    },
    handleScroll() {
      let _scrollTop = this.$el.scrollTop,
          _height = this.$el.querySelectorAll('ul')[0].offsetHeight,
          _contentHeight = this.$el.offsetHeight;

      if (_scrollTop / this.height - Math.floor(_scrollTop / this.height) > 0.5) {
        this.displayCount = Math.ceil(_scrollTop / this.height);
      } else {
        this.displayCount = Math.floor(_scrollTop / this.height);
      }

      if (this.lastScrollTop === null || Math.abs(_scrollTop - this.lastScrollTop) > this._max) {
          this.lastScrollTop = _scrollTop;
      } else {
          // console.log(_height, _scrollTop, _contentHeight, _height-_scrollTop-_contentHeight);
          if (this.to === this.list.length && _height - _scrollTop - _contentHeight < this.distance) {
            this.canScroll && this.loadmore(this.from, this.to);
          }
          return;
      }

      var _from = parseInt(_scrollTop / this.height) - this._above;
      if (_from < 0) {
          _from = 0;
      }
      var _to = _from + this._above + this._below + this._rowsInWindow;
      if (_to > this.list.length) {
          _to = this.list.length;
      }

      this.from = _from;
      this.to = _to;
      this.lineTopHeight = _from * this.height;
      this.lineBottomHeight = (this.list.length - _to) * this.height;
      if (typeof this.dispatchData === 'function') {
        this.dispatchData(this)
      }

      this.previewList = [];
      for (; _from < _to; _from++) {
          this.previewList.push(this.list[_from]);
      }
    },
    loadmore(from, to) {
      if (!this.canLoadmore) return;
      this.canLoadmore = false;
      setTimeout(() => {
        for(var i = 0; i < 200; i++) {
          this.list.push({
            title: 'item ' + COUNT++
          });
        }
        let _from = from, _to = to + this._below;
        this.previewList = [];
        for (; _from < _to; _from++) {
            this.previewList.push(this.list[_from])
        }
        this.lineBottomHeight = (this.list.length - _to) * this.height;
        this.handleScroll();

        this.canLoadmore = true;
      }, 2000)
    }
  },
  components: {}
}
</script>

<style lang="less">
.vue-list{
  width: 100%;
  height: 100%;
  overflow-y: auto;
  &::scroll-bar{
    width: 0;
  }
  ul {
    margin: 0;
    padding: 0;
    li{
      text-decoration: none;
      height: 44px;
      font-size: 14px;
      line-height: 3;
      text-align: left;
      padding-left: 15px;
      border-bottom: 1px solid #ddd;
      box-sizing: border-box;
      background: #fff;
      &.line-top, &.line-bottom{
        border-bottom: 0;
      }
    }
  }
  .load-more-gif{
    width: 100%;
    height: 44px;
    text-align: center;
    line-height: 44px;
    background: #fff;
    border-top: none;
    color: #48B884;
  }
}
</style>
