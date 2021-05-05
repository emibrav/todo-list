<template>
  <div class="container max-w-md mx-auto">
    <div class="">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        type="text"
        class="border
      w-full my-4 placeholder-gray-500 placeholder-opacity-50"
        placeholder="Nueva tarea..."
      />
    </div>
    <div
      v-for="(todo, index) in todosFiltered"
      :key="todo.id"
      class="flex justify-between"
    >
      <div class="flex items-center">
        <input
          type="checkbox"
          v-model="todo.completed"
          class="mr-3 w-2.5 h-2.5"
        />
        <div
          v-if="!todo.edited"
          @dblclick="editTodo(todo)"
          v-bind:class="{ linethrough: todo.completed }"
          class=""
        >
          {{ todo.title }}
        </div>
        <div v-else>
          <input
            v-model="todo.title"
            type="text"
            v-focus
            class="border flex items-center mt-1"
            @keyup.esc="cancelEdit(todo)"
            @keyup.enter="doneEdit(todo)"
            @blur="doneEdit(todo)"
          />
        </div>
      </div>
      <div
        v-show="!todo.edited"
        @click="deleteTodo(index)"
        class="cursor-pointer my-1 pb-1"
      >
        &times;
      </div>
    </div>
    <hr />
    <div class="container flex justify-between mt-2">
      <label>
        <input type="checkbox" :checked="!anyRemaining" @change="checkAll" />
        Check All
      </label>
      {{ remaining }} items left
    </div>
    <hr />
    <div class="flex mt-2">
      <button
        class="mr-2 border px-2"
        :class="{ filter: filter == 'all' }"
        @click="filter = 'all'"
      >
        All
      </button>
      <button
        class="mr-2 border px-2"
        :class="{ filter: filter == 'active' }"
        @click="filter = 'active'"
      >
        Active
      </button>
      <button
        class="border px-2"
        :class="{ filter: filter == 'completed' }"
        @click="filter = 'completed'"
      >
        Completed
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "TodoList",
  data() {
    return {
      newTodo: "",
      idNewTodo: 1,
      beforeEditCache: "",
      filter: "all",
      todos: [],
    }
  },
  directives: {
    focus: {
      // Definición de directiva
      inserted: function(el) {
        el.focus()
      },
    },
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length
    },
    anyRemaining() {
      return this.remaining > 0
    },
    todosFiltered() {
      if (this.filter == "all") {
        return this.todos
      } else if (this.filter == "active") {
        return this.todos.filter((todo) => !todo.completed)
      } else if (this.filter == "completed") {
        return this.todos.filter((todo) => todo.completed)
      }
      return this.todos
    },
  },
  mounted() {
    if (localStorage.todos) {
      this.todos = JSON.parse(localStorage.todos)
    }
  },
  watch: {
    todos: {
      handler(newTodos) {
        localStorage.todos = JSON.stringify(newTodos)
      },
      deep: true,
    },
  },
  methods: {
    addTodo() {
      if (this.newTodo.trim().length == 0) {
        alert("Debes completar este campo!")
      } else {
        this.todos.push({
          title: this.newTodo,
          id: this.idNewTodo,
          edited: false,
          completed: false,
        }),
          this.idNewTodo++
        this.newTodo = ""
      }
    },
    editTodo(todo) {
      this.beforeEditCache = todo.title
      todo.edited = true
    },
    cancelEdit(todo) {
      todo.edited = false
      todo.title = this.beforeEditCache
    },
    doneEdit(todo) {
      if (todo.title.trim().length == 0) {
        todo.title = this.beforeEditCache
      }
      todo.edited = false
    },
    deleteTodo(index) {
      this.todos.splice(index, 1)
    },
    checkAll() {
      this.todos.forEach((todo) => (todo.completed = event.target.checked))
    },
  },
}
</script>
<style scoped>
.linethrough {
  text-decoration: line-through;
}
.filter {
  background-color: #9bffd2;
}
</style>
