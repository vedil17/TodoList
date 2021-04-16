<template>
  <div id="app">
    <todos></todos>
    <todos></todos>
    <todos></todos>
  </div>
</template>

<script>
import Todos from './components/Todos'
export default {
  name: 'App',
  components: {
    Todos
  }
}
</script>
