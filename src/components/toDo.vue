<template>
  <div class="all">
    <h2 class="head">{{ name }}</h2>
    <input v-model="newTodo" type="text" class="todo-input" @keyup.enter="addTodo" placeholder="What needs to be done">
    <div v-for="(todo, index) in todos" :key="todo.id" class="todo-item">
        <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed">
            <div  v-if="!todo.editing" @click="editTodo(todo)" class="todo-item-label" :class="{ completed: todo.completed }">
                {{ todo.title }}
            </div>
           <input @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus v-else class="todo-item-edit" type="text" v-model="todo.title"> 
        </div>
        <div class="remove-item" @click="removeTodo(index)"> &times;</div>
    </div>

    <div class="another-container">
        <div> <label> <input type="checkbox" :checked="!noneRemaining" @change="checkAllTodos">Check All </label> </div>
        <div>{{ remaining }} items left</div>
    </div>

    
  </div>
</template>

<script>



export default {
name:'todo-list',


data(){
    return{
        name: 'LIST OF THINGS TO DO' ,
        newTodo:'',
        todoId: 3 ,
        beforEditCache:'',
        filter: 'all' ,
        editedTodo: null,
        todos: []
    }  
},
computed:{
        remaining(){
            return this.todos.filter(todo =>!todo.completed).length
        },
        noneRemaining(){
            return this.remaining != 0
        },
        
    },
directives:{
    focus: {
        inserted: function (el) {
            el.focus()
        }
    }
},
methods:{
    addTodo(){     
        //to prevent empty input   
        if (this.newTodo.trim().length == 0) {
        return
    }
    //posting new todo to the todo list
    this.todos.push({
        id: this.todoId,
        title: this.newTodo,
        completed: false
    })
        this.newTodo = ''
        // this.todoId++
    },
    editTodo(todo){
        this.beforEditCache = todo.title
        todo.editing = true  
    },
    //to remove the box from the input once editing is done
    doneEdit(todo){
        if (todo.title.trim().length == 0) {
        todo.title = this.beforeEditCache
    }
        todo.editing = false 
    },
    // cancelEdit(todo){
    //     todo.title = beforEditCache
    //     todo.editing = false
    // },
    //deleting each todos in the list
    removeTodo(index) {
        this.todos.splice(index, 1)
    },
    checkAllTodos(event){
        this.todos.forEach((todo) => todo.completed = event.target.checked)
    }
 }
}
</script>

<style lang="scss">
.head{
    text-decoration: underline;
    color: #2c3e50;
    margin-top: 70px;
}
.todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 20px ;
    margin-top: 50px ; 
   
    &:focus {
        outline:0;
    }
}
.todo-item{
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
}
.remove-item{
    cursor: pointer;
    margin-left: 14px;
    &:hover{
        color: black;
    }
}
.todo-item-left{
    display: flex;
    align-items: center;
}

.todo-item-label{
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}

.todo-item-edit{
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    padding: 10px;
    border: 1px solid#ccc;
    font-family: 'avenir', Helvetica, sans-serif;

    &:focus {
        outline: none
    }
}

.completed{
    text-decoration: line-through;
    color: grey;
}

.another-container{
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-top: 14px;
}

button{
    font-size: 14px;
    background-color: white;
    appearance: none;

    &:focus {
        outline: none;
    }
}
.active {
    background-color: lightgreen;
    
}
</style>

<!-- todosFiltered(){
    if (this.filter = 'all') {
      return this.todos  
    }else if (this.filter = 'active') {
        return this.todos.filter(todo => !todo.completed)
    }else if (this.filter = 'completed') {
        return this.todos.filter(todo => todo.completed)
    }
    return this.todos
},
showClearCompleted(){
    return this.todos.filter(todo => todo.completed).lenght > 0
} -->

//outcoded todo list components
// {
//     'id': 1,
//     'title': 'Go to gym',
//     'completed' : false ,
//     'editing': false
// },
// {
//     'id': 2,
//     'title': 'Gocery Shopping',
//     'completed' : false ,
//     'editing': false
// }
