<template>
  <div class="todo-item">
    <div class="todo-item-left">
      <input type="checkbox" v-model="completed" @change='doneEdit'/>
      <div
        v-if="!editing"
        @dblclick="editTodo"
        class="todo-item-label"
        :class="{completed:completed}"
      >{{ title }}</div>
      <input
        v-else
        type="text"
        class="todo-item-edit"
        @blur="doneEdit"
        @keyup.esc="cancelEdit"
        @keyup.enter="doneEdit"
        v-model="title"
        v-focus
      />
    </div>
    <div>
      <button @click='pluralize'>Plural</button>
      <span class="remove-item" @click="removeTodo(index)">&times;</span>
    </div>
  </div>
</template>
<script>
export default {
  name: "todo-item",
  props:{
    todo:{
      type:Object,
      required:true,
    },
    index:{
      type:Number,
      required:true,
    },
    checkAll:{
      type:Boolean,
      required:true,
    }
  },
  data(){
    return {
      'id': this.todo.id,
      'title': this.todo.title,
      'completed': this.todo.completed,
      'editing': this.todo.editing,
      'beforeEditCache':'',
    }
  },
  created(){
    eventBus.$on('pluralize',this.handlePluralize)
  }, 
  beforeDestroy(){
    eventBus.$off('pluralize',this.handlePluralize)
  },
  watch:{
    checkAll(){
      if(this.checkAll){
        this.completed = true
      }else{
        this.completed = this.todo.completed
      }
    }
  },
  methods:{
    removeTodo(index){
      eventBus.$emit('removedTodo',index)
    },
    editTodo() {
      this.beforeEditCache = this.title;
      this.editing = true;
    },
    cancelEdit() {
      this.title = this.beforeEditCache;
      this.editing = false;
    },
    doneEdit() {
      if (this.title.length == 0) this.title = this.beforeEditCache;
      this.editing = false;
      eventBus.$emit('finishedEdit',{
        'index':this.index,
        'todo': {
          'id': this.id,
          'title': this.title,
          'completed':this.completed,
          'editing': this.editing,
        }
      })
    },
    pluralize(){
      eventBus.$emit('pluralize')
    },
    handlePluralize(){
      this.title = this.title +'s'
      eventBus.$emit('finishedEdit',{
        'index':this.index,
        'todo': {
          'id': this.id,
          'title': this.title,
          'completed':this.completed,
          'editing': this.editing,
        }
      })  
    }
  }
};
</script>