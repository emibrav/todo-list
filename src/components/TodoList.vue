<template>
  <div class="container max-w-md mx-auto px-10 pb-4">
    <div class="">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        type="text"
        class="border
      w-full mb-3 mt-4 placeholder-gray-500 placeholder-opacity-50 px-2 py-1 text-sm focus:outline-none focus:ring-1 focus:ring-green-500"
        placeholder="Nueva tarea...[2 click pa modificar]"
      />
      <div class="mb-3" v-if="noActive">
        "No hay tareas activas"
      </div>
      <div class="mb-3" v-if="noDone">
        "No hay tareas hechas aun"
      </div>
    </div>
    <todo-item
      v-for="todo in todosFiltered"
      :key="todo.id"
      class="flex justify-between"
      :todo="todo"
      :checkAll="!anyRemaining"
      @deletedTodo="deleteTodo"
      @finishedEdit="doneEdit"
    >
    </todo-item>
    <hr />
    <div class="container flex justify-between mt-2">
      <label class="mb-2">
        <input
          type="checkbox"
          :checked="!anyRemaining"
          @change="checkAllTodos"
        />
        Tildar todas
      </label>
      Falta(n) {{ remaining }} por hacer
    </div>
    <hr />
    <div class="flex mt-2 justify-between">
      <div>
        <button
          class="mr-2 border px-2 text-xs"
          :class="{ filter: filter == 'all' }"
          @click="noTaskYet"
        >
          Todas
        </button>
        <button
          class="mr-2 border px-2 text-xs"
          :class="{ filter: filter == 'active' }"
          @click="activeTaskFilter"
        >
          No hechas
        </button>
        <button
          class="border px-2 text-xs"
          :class="{ filter: filter == 'completed' }"
          @click="completedActiveFilter"
        >
          Hechas
        </button>
      </div>
      <div>
        <button
          class="border px-1 py-1 text-xs bg-gray-100 hover:bg-gray-200"
          @click="cleanAll"
        >
          Limpiar
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import TodoItem from "./TodoItem.vue"

export default {
  name: "TodoList",
  components: { TodoItem },
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
    doneEdit(data) {
      const index = this.todos.findIndex((item) => item.id == data.id)
      this.todos.splice(index, 1, data)
    },
    deleteTodo(id) {
      const index = this.todos.findIndex((item) => item.id == id)
      this.todos.splice(index, 1)
    },
    checkAllTodos() {
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
    cleanAll() {
      //this.todos.splice(0)
      this.todos = []
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
