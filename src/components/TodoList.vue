<template>
  <div class="container max-w-md mx-auto px-10">
    <div class="">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        type="text"
        class="border
      w-full mb-3 mt-4 placeholder-gray-500 placeholder-opacity-50"
        placeholder="Nueva tarea...[2 click pa modificar]"
      />
      <!-- <div class="mb-3" v-if="todos.length == 0">
        Probame, soy alta app
      </div> -->
      <div class="mb-3" v-if="noActive">
        "No hay tareas activas"
      </div>
      <div class="mb-3" v-if="noDone">
        "No hay tareas hechas aun"
      </div>
    </div>
    <div
      v-for="(todo, index) in todosFiltered"
      :key="todo.id"
      class="flex justify-between"
    >
      <div
        class="flex items-center appearance-none checked:bg-blue-600 checked:border-transparent"
      >
        <input type="checkbox" v-model="todo.completed" class="mr-3" />
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
        Tildar todas
      </label>
      Falta(n) {{ remaining }} por hacer
    </div>
    <hr />
    <div class="flex mt-2">
      <button
        class="mr-2 border px-2"
        :class="{ filter: filter == 'all' }"
        @click="noTaskYet"
      >
        Todas
      </button>
      <button
        class="mr-2 border px-2"
        :class="{ filter: filter == 'active' }"
        @click="activeTaskFilter"
      >
        No hechas
      </button>
      <button
        class="border px-2"
        :class="{ filter: filter == 'completed' }"
        @click="completedActiveFilter"
      >
        Hechas
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
      noActive: false,
      noDone: false,
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
    activeTaskFilter() {
      this.filter = "active"
      if (this.todos.length == 0) {
        this.noActive = false
      } else {
        this.noActive = true
      }
      if (this.remaining > 0) {
        this.noActive = false
      }
      this.noDone = false
    },
    completedActiveFilter() {
      this.filter = "completed"
      if (this.todos.length == 0) {
        this.noDone = false
      }
      if (this.remaining == 0) {
        this.noDone = false
      }
      this.noActive = false
      if (this.todos.filter((todo) => todo.completed).length == 0) {
        this.noDone = true
      }
    },
    noTaskYet() {
      this.filter = "all"
      this.noDone = false
      this.noActive = false
    },
  },
}
</script>
<style scoped>
.linethrough {
  text-decoration: line-through;
}
.filter {
  background-color: #41b883;
  color: rgba(249, 250, 251);
}
</style>
