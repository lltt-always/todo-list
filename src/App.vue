<template>
    <div id="app">
        <h1>{{ header }}</h1>
        <div class="search">
            <input type="text" name="search" v-model="keys" :value="keys" placeholder="Key Words" @keyup.delete="search">
            <button @click="search">search</button>
        </div>
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
        <div v-if="showResult" class="result">{{ searchResult }}</div>
        <!--For Modal-->
        <!-- use the modal component, pass in the prop -->
        <modal v-if="showModal" @close="showModal = false">
            <h3 slot="header">{{ modalHeader }}</h3>
            <span slot="body"></span>
            <span slot="footer"></span>
        </modal>
    </div>
</template>

<script>
    import Modal from './components/Modal'

    export default {
        name: 'todolist',
        data: function() {
            return {
                header: 'Todos',
                // items为展示数据，初始化为localStorage.items
                items: localStorage.items ? JSON.parse(localStorage.items) : [],
                // localItems为数据库数据，初始化为localStorage.items
                localItems: localStorage.items ? JSON.parse(localStorage.items) : [],
                newItem: '',
                showModal: false,
                keys: '',
                modalHeader: '',
                searchResult: '',
                showResult: ''
            }
        },
        components: {
            Modal
        },
        methods: {
            // 更新展示数据
            search: function(){
                if(this.localItems.length){
                    this.items = [];
                    var reg = new RegExp(this.keys, 'i');
                    for(var value of this.localItems){
                        if(value.content.search(reg)!=-1){
                            this.items.push(value)
                        }
                    }
                } else {
                    this.showResult = true;
                    this.searchResult = 'There is no result';
                };
                if(this.items.length){
                    this.showResult = false;
                } else {
                    this.showResult = true;
                    this.searchResult = 'There is no result';
                };
            },
            // 完成／未完成标识操作需要对数据库进行修改，然后再更新展示数据
            finished: function (item) {
                this.localItems.forEach(function(val, index, arr){
                    if(val.content == item.content){
                        val.isFinished = true;
                    }
                });
                this.update(this.localItems);
                this.search();
            },
            unfinished: function (item) {
                this.localItems.forEach(function(val, index, arr){
                    if(val.content == item.content){
                        val.isFinished = false;
                    }
                });
                this.update(this.localItems);
                this.search()
            },
            // 添加操作需要对数据库进行修改，然后再更新展示数据
            addItem: function () {
                if (this.newItem) {
                    this.localItems.push({content: this.newItem, isFinished: false})
                    this.update(this.localItems);
                    this.search();
                    this.newItem = ''
                } else {
                    this.showModal = true;
                    this.modalHeader = 'Please input a new item';
                }
            },
            // 删除操作同样需要对数据库进行修改，然后再更新展示数据
            delItem: function (item) {
                var _this = this;
                this.localItems.forEach(function(val, index, arr){
                    if(val.content == item.content){
                        _this.localItems.splice(index, 1)
                    }
                });
                this.update(this.localItems);
                this.search();
            },
            // 写数据库
            update: function (items) {
                localStorage.setItem('items', JSON.stringify(items));
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
        background-color: #DCACBF;
        margin: 19px 20px 19px 20px;
        border-radius: 100%;
        position: absolute;
    }
    .todolist li label {
        padding: 15px 60px 15px 15px;
        margin-left: 45px;
        display: block;
        height: 24px;
    }
    .todolist li button {
        line-height: 54px;
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

    .todolist li:hover button {
        color: #af5b5e;
        transition: color 0.4s;
        display: block;
    }
    input:focus, button:focus {
        outline: none;
    }
    .search {
        height: 30px;
        border: solid 1px #aaaaaa;
        display: inline-block;
        margin: 10px auto;
    }
    .search button {
        font-size: 20px;
        background-color: #aaaaaa;
        line-height: 30px;
        height: 30px;
        color: #ffffff;
        border: none;
    }
    .search input {
        padding: 0 0 0 5px;
        border: none;
        background-color: rgba(0,0,0,0);
        font-size: 20px;
        height: 20px;
    }
    .result {
        margin-top: 10px;
    }
</style>
