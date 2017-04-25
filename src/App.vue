<template>
  <div id="app">
    <h1>{{ header }}</h1>
    <div class="newitem">
      <input v-model="newItem" placeholder="What needs to be done?">
      <button @click="addItem" class="addbutton">Add</button>
    </div>
    <ul class="todolist">
      <li v-for="item in items">
        <input type="checkbox">
        <span class="unchecked" @click="finished(item)"></span>
        <span :class="{checked: item.isFinished}" @click="unfinished(item)"></span>
        <label :class="{isfinished: item.isFinished}">{{ item.content }}</label>
        <button @click="delItem(item)">x</button>
      </li>
    </ul>
    <!--For Modal-->
    <!-- use the modal component, pass in the prop -->
    <modal v-if="showModal" @close="showModal = false">
      <!--
        you can use custom content here to overwrite
        default content
      -->
      <h3 slot="header">Please input a new item</h3>
      <span slot="body"></span>
      <span slot="footer"></span>
    </modal>
  </div>
</template>

<script>
import Modal from './components/Modal'
var storage = window.localStorage

export default {
  name: 'todolist',
  data: function() {
    return {
      header: 'Todos',
      items: [],
      newItem: '',
      showModal: false
    }
  },
  // data: function() {
  //   return {
  //     header: 'Todos',
  //     items: window.JSON.parse(storage.items)||[],
  //     newItem: '',
  //     showModal: false
  //   }
  // },
  components: {
    Modal
  },
  methods: {
    finished: function (item) {
      item.isFinished = true
      this.update()
    },
    unfinished: function (item) {
      item.isFinished = false
      this.update()
    },
    addItem: function () {
      if (this.newItem) {
        this.items.push({content: this.newItem, isFinished: false})
        this.update()
        this.newItem = ''
      } else {
        this.showModal = true
      }
    },
    delItem: function (item) {
      let index = this.items.indexOf(item)
      if (index > -1) {
        return this.items.splice(index, 1)
      }
    },
    update: function () {
      var localitems = window.JSON.stringify(this.items)
      storage.setItem('items', localitems)
    }
  }
}
</script>

<style>
body {
  background-color: #f5f5f5;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto 0 auto;
  max-width: 550px;
  min-width: 230px;
}
.isfinished {
  text-decoration: line-through;
}
.addbutton {
  background-color: #04b10b;
  border-radius: 6px;
  width: 40px;
  height: 32px;
  color: #ffffff;
  border: none;
}
.addbutton:hover {
  background-color: #036f07;
}
.newitem {
  padding: 16px 16px 16px 60px;
  position: relative;
  background-color: #ffffff;
}
.newitem input {
  font-size: 24px;
  font-style: italic;
  width: 100%;
  border: none;
}
.newitem button {
  position: absolute;
  right: 0;
  top: 0;
  margin: 16px 16px 16px 0;
}
.todolist {
  text-align: left;
  padding: 0;
  margin: 0;
  list-style: none;
}
.todolist li {
  font-size: 24px;
  background-color: #ffffff;
  position: relative;
  border-bottom: 1px solid #ededed;
}
.todolist li input {
  left: 0;
  top: 0;
  margin: 23px 24px 23px 24px;
  position: absolute;
  z-index: 2;
  display: none;
}
.todolist li .unchecked {
  height: 30px;
  width: 30px;
  margin: 13px 14px 13px 14px;
  border-radius: 100%;
  border: 1px #ededed solid;
  position: absolute;
}
.todolist li .checked {
  height: 20px;
  width: 20px;
  background-color: #00ff00;
  margin: 19px 20px 19px 20px;
  border-radius: 100%;
  position: absolute;
}
.todolist li label {
  padding: 15px 60px 15px 15px;
  margin-left: 45px;
  display: block;
  line-height: 1.2;
}
.todolist li button {
  height: 40px;
  width: 40px;
  border: none;
  padding: 0;
  font-size: 30px;
  color: #ffffff;
  position: absolute;
  background-color: #ffffff;
  right:15px;
  top: 0;
}
.todolist li button:hover {
  color: #af5b5e;
  transition: color 0.4s;
}
.todolisyt li:hover button {
  display: block;
}
input:focus, button:focus {
  outline: none;
}
</style>
