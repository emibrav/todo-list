<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png" class="mt-20" />
    <todo-list />
  </div>
</template>

<script>
import TodoList from "@/components/TodoList.vue"

export default {
  name: "App",
  components: { TodoList },
}
</script>
TodoList

<style>
#app {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif, Cochin,
    Georgia, Times, "Times New Roman", serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  margin-top: auto;
}
img {
  width: 70px;
  height: 70px;
}
</style>
