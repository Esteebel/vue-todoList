<template>
  <div id="app" class="container">
    <!-- <toDo/> -->
    <todos/>
  </div>
</template>

<script>
// import toDo from './components/toDo.vue'
import todos from './components/todos.vue'



export default {



  components:{
    // toDo,
    todos
  }
}

</script>

<style scoped>
*{
  box-sizing: border-box;
}
#app{
  font-size: 22px;

}
.container{
  max-width: 600px;
  margin: 0 auto;
}
</style>
