<template>
  <div class="todo-container">
    <div class="todo-wrap">
      <!-- <Header :addTodo="addTodo"></Header> -->
      <Header @addTodo="addTodo"></Header>
      <!-- props -->
      <!-- <List :todos="todos" :updateOne="updateOne" :deleteOne="deleteOne"></List> -->
      <!-- 全局事件总线和pubSub -->
      <List :todos="todos"></List>
      <!-- <Footer :todos="todos" :updateAll="updateAll" :deleteAll="deleteAll"></Footer> -->
      <Footer :todos="todos" :updateAll="updateAll"></Footer>
    </div>
  </div>
</template>

<script>
import PubSub from 'pubsub-js'
import Header from '@/components/Header'
import List from '@/components/List'
import Footer from '@/components/Footer'
export default {
  name: "",
  components:{
    Header,
    List,
    Footer
  },
  mounted(){
    // 在App当中找到总线，给总线绑定一个事件'deleteOne'
    this.$bus.$on('deleteOne',this.deleteOne)

    this.$bus.$on('deleteAll',this.deleteAll)

    //消息订阅处理修改单个
    PubSub.subscribe('heihei',this.updateOne)
  },
  data(){
    return {
      // todos:[
      //   {id:1,content:'抽烟',isOver:false},
      //   {id:2,content:'喝酒',isOver:true},
      //   {id:3,content:'烫头',isOver:true}
      // ]
      // 
      todos: JSON.parse(localStorage.getItem('TODOS_KEY')) || []
    }
  },

  watch:{
    todos:{
      //代表深度监视
      //一般监视和深度监视
      //一般监视监视的是数组本身的数据，但是数组内部对象的数据监视不到
      //深度监视可以监视到数组本身的数据，也可以监视到数组内部对象的数据
      deep:true,
      handler(newVal,oldVal){
        //只要todos数据发生变化，就把变化后的数据存储到localStorage当中
        //localStorage 是前端本地存储的方案，是一个小型的数据库，存储到localStorage当中的东西都会自动转化为字符串
        //localStorage当中有4个Api
        // localStorage.setItem('键',值)   //给localStorage存储数据
        // localStorage.getItem('键')       //获取localStorage当中某个数据 能获取到就获取到  获取不到返回null 不会影响其它的
        // localStorage.removeItem('键')   //删除localStorage当中某个数据
        // localStorage.clear()           //清空localStorage所有的数据


        // 对象数据类型转基本数据类型
        // 计算  比较  全部转基本
        //判等的时候   判等如果都是对象 判地址  如果有一个不是对象类型 那转基本
        // function fn(){
        //   console.log(111)
        // }
        // console.log([1,2,3] + 100)  // '1,2,3100'
        // console.log({name:'zly'} + 100) //'[object  Object]100'
        // console.log(fn + 100)//  'function fn(){console.log(111)}100'

        //1 数组转基本  去掉中括号 中间留下什么 就带引号     [1,2,3]   '1,2,3'
        //2 对象转基本  固定的 '[object  Object]'
        //3、函数转基本  固定的 函数整体加字符串 

        // localStorage.setItem('TODOS_KEY',newVal)  //不能直接存对象数据类型，因为对象数据全部都会私自转基本，数据就变味了
        localStorage.setItem('TODOS_KEY',JSON.stringify(newVal))  
      }
    }
  },



  methods:{
    addTodo(todo){
      this.todos.unshift(todo)
    },
    // msg是消息订阅和发布 传递过来的消息名称，即使不用 也要占位
    updateOne(msg,index){
      this.todos[index].isOver = !this.todos[index].isOver
    },
    
    deleteOne(index){
      this.todos.splice(index,1)
    },
    updateAll(val){
      this.todos.forEach(item => item.isOver = val)
    },
    deleteAll(){
      //把没打勾的过滤出来组成新数组，把原数组修改为这个新数组
      this.todos = this.todos.filter(item => !item.isOver)
    }
  }
};
</script>

<style scoped>
/*app*/
.todo-container {
  width: 600px;
  margin: 0 auto;
}
.todo-container .todo-wrap {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
