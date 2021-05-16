<template>
  <div>
    <div
      class="flex items-center appearance-none checked:bg-blue-600 checked:border-transparent"
    >
      <input
        type="checkbox"
        v-model="completed"
        class="mr-3"
        @change="doneEdit"
      />
      <div
        v-if="!edited"
        @dblclick="editTodo"
        v-bind:class="{ linethrough: completed }"
      >
        {{ title }}
      </div>
      <div v-else>
        <input
          v-model="title"
          type="text"
          v-focus
          class="border flex items-center mt-1 focus:outline-none"
          @keyup.esc="cancelEdit"
          @keyup.enter="doneEdit"
          @blur="doneEdit"
        />
      </div>
    </div>
    <div
      v-show="!edited"
      @click="deleteTodo(todo.id)"
      class="cursor-pointer my-1 pb-1"
    >
      &times;
    </div>
  </div>
</template>

<script>
export default {
  name: "todo-item",
  props: {
    todo: {
      type: Object,
      required: true,
    },
    checkAll: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      id: this.todo.id,
      title: this.todo.title,
      completed: this.todo.completed,
      edited: this.todo.edited,
      beforeEditCache: "",
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
  watch: {
    checkAll() {
      this.completed = this.checkAll ? true : this.todo.completed
    },
  },
  methods: {
    deleteTodo(index) {
      this.$emit("deletedTodo", index)
    },
    editTodo() {
      this.beforeEditCache = this.title
      this.edited = true
    },
    cancelEdit() {
      this.edited = false
      this.title = this.beforeEditCache
    },
    doneEdit() {
      if (this.title.trim().length == 0) {
        this.title = this.beforeEditCache
      }
      this.edited = false
      this.$emit("finishedEdit", {
        id: this.id,
        title: this.title,
        completed: this.completed,
        edited: this.edited,
      })
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
