<template>
  <div id="app">
    <h1 class="title">vue-list</h1>
    <p>An infinite load more list component.</p>
    <hr>
    <div class="content">
      <div class="preview">
        <div class="preview-content">
          <vue-list :list.sync='list'></vue-list>
        </div>
      </div>
      <div class="setting">
        <p>Total items: {{list.length}}</p>
        <p>Total items: {{from}}</p>
        <p>Total items: {{to}}</p>
        <p>Total items: {{top}}</p>
        <p>Total items: {{bottom}}</p>
        <p>Total items: {{list.length}}</p>
      </div>
    </div>
  </div>
</template>

<script>
import vueList from '../lib/vue-list';

export default {
  name: 'app',
  data() {
    return {
      list: [],
      from: 0,
      to: 0,
      top: 0,
      bottom: 0
    }
  },
  created() {
    for(var i = 0; i < 200; i++) {
      this.list.push({
        title: 'list ' + new Date().getTime()
      });
    }

    this.$on('scrollData', (from, to, top, bottom) => {
      console.log('in');
      this.from = from;
      this.to = to;
      this.top = top;
      this.bottom = bottom;
    })
  },
  components: {
    vueList
  }
}
</script>

<style lang="less">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  .title{
    line-height: 50px;
  }
  .content{
    display: flex;
    width: 800px;
    height: 650px;
    margin: 0 auto;
    &>div{
      flex: 1;
      height: 100%;
    }
    .preview{
      background: url("~assets/iphone.png") center center no-repeat;
      background-size: 324px 642px;
      position: relative;
      .preview-content{
        width: 256px;
        height: 464px;
        position: absolute;
        left: 72px;
        top: 91px;
      }
    }
  }
}
</style>
