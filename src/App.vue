<template>
  <div id="app">
    <h1 class="title">vue-list</h1>
    <div class="content">
      <div class="preview">
        <vue-list :list.sync='list'></vue-list>
      </div>
      <div class="setting">
        setting
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
      list: []
    }
  },
  created() {
    for(var i = 0; i < 200; i++) {
      this.list.push({
        title: 'list ' + new Date().getTime()
      });
    }
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
  margin-top: 60px;
  .title{
    line-height: 50px;
  }
  .content{
    display: flex;
    width: 800px;
    height: 500px;
    margin: 0 auto;
    &>div{
      flex: 1;
      height: 100%;
    }
  }
}
</style>
