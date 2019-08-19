<template>
  <div id="app">
    <u-utodo></u-utodo>
    <!-- <u-todo></u-todo> -->
  </div>
</template>

<script>
import todo from "./components/todo.vue"
import utodo from "./components/todomvc.vue"
export default {
  name: 'app',
  data () {
    return {
    
    }
  },
  components:{
    "u-todo":todo,
    "u-utodo":utodo
  }
}
</script>

<style>
body{
  font-family: '楷体';
  font-size: 20px;
  /* background-image: url("./assets/test.jpg"); */
  /* position: absolute;
  height: 100vh;
  width: 100%;
  background-repeat: no-repeat;
  background-size: cover; */
  /* background-color: rgba(91, 235, 245, 0.363); */
}
</style>
