<template>
  <section class="todoapp">
    <header class="header">
      <h1>Todos</h1>
      <input type="text" class="new-todo" placeholder="Ajouter un tache" v-model="newTodo" @keyup.enter="addTodo()">
    </header>
    <div class="main">
      <input id="toggle-all" type="checkbox" class="toggle-all" v-model="allDone">
      <label for="toggle-all">Mark all as complete</label>
      <ul class="todo-list">
        <li class="todo" v-for="todo in filteredTodos" :class="{completed : todo.completed, editing : todo === editing}">
          <div class="view">
            <input class="toggle" type="checkbox" v-model="todo.completed">
            <label @dblclick="editTodo(todo)">{{ todo.name }}</label>
            <button class="destroy" @click.prevent="deleteTodo(todo)"></button>
          </div>
          <input type="text" class="edit" v-model="todo.name" @keyup.enter="doneEdit" @blur="doneEdit" @keyup.esc="cancelEdit" v-focus="todo === editing">
        </li>
      </ul>
    </div>
    <footer class="footer" v-show="todos.length > 0">
      <span class="todo-count"><strong>{{ remainingTodosCount }}</strong>Tache à faire</span>
      <ul class="filters">
        <li><a href="#/all" :class="{ selected : filter === 'all' }" @click.prevent="filter = 'all'">Toutes</a></li>
        <li><a href="#/active" :class="{ selected : filter === 'todo' }" @click.prevent="filter = 'todo'">A Faire</a></li>
        <li><a href="#/completed" :class="{ selected : filter === 'done' }" @click.prevent="filter = 'done'">Faites</a></li>
      </ul>
      <button class="clear-completed" v-show="completedTodosCount > 0" @click.prevent="deleteCompleted">Clear completed</button>
    </footer>
  </section>
</template>

<script>
import Vue from 'vue'
import store from './TodosStore'
import Vuex from 'vuex'

export default {
  store: store,
  data () {
    return {
      newTodo: '',
      filter: 'all',
      editing: null,
      oldTodo: ''
    }
  },
  methods: {
    ...Vuex.mapActions({
      addTodoStore: 'addTodo',
      deleteTodo: 'deleteTodo'
    }),
    addTodo () {
      this.addTodoStore(this.newTodo)
      this.newTodo = ''
    },
    deleteCompleted () {
      this.todos = this.todos.filter(todo => !todo.completed)
    },
    editTodo (todo) {
      this.editing = todo
      this.oldTodo = todo.name
    },
    doneEdit () {
      this.editing = null
    },
    cancelEdit () {
      this.editing.name = this.oldTodo
      this.doneEdit()
    }
  },
  computed: {
    ...Vuex.mapGetters([
      'todos',
      'remainingTodosCount',
      'completedTodosCount',
      'remainingTodos',
      'completedTodos'
    ]),
    allDone: {
      get () {
        return this.remaining === 0
      },
      set (value) {
        this.todos.forEach(todo => {
          todo.completed = value
        })
      }
    },
    filteredTodos () {
      if (this.filter === 'todo') {
        return this.remainingTodos
      } else if (this.filter === 'done') {
        return this.completedTodos
      }
      return this.todos
    }
  },
  directives: {
    focus (el, value) {
      Vue.nextTick(_ => {
        el.focus()
      })
    }
  }
}
</script>

<style src="./todos.css"></style>
