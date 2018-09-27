<template>
  <div id="app">
    <h4>{{ title }}</h4>
    <input type="text" @keyup.enter="addNewList" v-model="newItem" placeholder="请添加事项">
    <ul>
      <li v-for="(item, index) in items">
        <span v-if="!item.edit" @click="changeList(index)">{{item.content}}</span>
        <input v-else type="text" v-model="item.content" @blur="finish(index)" @keyup.enter="finish(index)">
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data: function () {
    return {
      title: 'This is a todo list',
      items: JSON.parse(localStorage.getItem('items')) || [],  //获取本地缓存中的items
      newItem: ''
    }
  },
  methods: {
    addNewList: function () {
      //输入框不为空时，添加到列表
      if(this.newItem){
        this.items.push({
          content: this.newItem,
          edit: false
        });
      }
      this.newItem = '';
    },
    changeList: function (index) {
      //改变已存在的列表项，变成可编辑状态，其他项变成不可编辑状态
      var i = index;
      var thisArg = this;
      this.items.map(function (currentValue, index) {
        if(index == i){
          thisArg.items[index].edit = true;
        }else {
          thisArg.items[index].edit = false;
        }
      }, thisArg);
    },
    finish: function (index) {
      //完成修改，变成不可编辑状态
      if(this.items[index].content == ''){
        this.items.splice(index, 1);
      }
      this.items[index].edit = false;
    }
  },
  watch: {
    items: {
      handler: function (items) {
        //深度监听items的值，有变化时，将其存入本地缓存
        var val = JSON.stringify(items);
        localStorage.setItem('items', val);
      },
      deep: true
    }
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin: 60px;
  }
</style>
