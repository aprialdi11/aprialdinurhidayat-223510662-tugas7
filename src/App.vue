<template>
  <div class="app">
    <h1>Todo List</h1>
    <div class="input-container">
      <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new task" />
      <button @click="addTodo">Add</button>
    </div>
    <div class="filter-buttons">
      <button :class="{ active: filter === 'all' }" @click="filter = 'all'">All Tasks</button>
      <button :class="{ active: filter === 'unfinished' }" @click="filter = 'unfinished'">Unfinished Tasks</button>
      <button :class="{ active: filter === 'finished' }" @click="filter = 'finished'">Completed Tasks</button>
    </div>
    <ul>
      <li v-for="(todo, index) in filteredTodos" :key="index" :class="{ completed: todo.completed }">
        <div class="todo-item">
          <input type="checkbox" :checked="todo.completed" @change="toggleTodoStatus(todo)" />
          <span>{{ todo.text }}</span>
        </div>
        <button @click="removeTodo(index)">Delete</button>
      </li>
    </ul>
    <div v-if="filter !== 'all'">
      <button class="delete-all" @click="deleteAll">Delete All</button>
    </div>
    <p v-if="filter === 'unfinished'" class="task-count">Total Unfinished Tasks: {{ unfinishedTodosCount }}</p>
    <p v-if="filter === 'finished'" class="task-count">Total Completed Tasks: {{ finishedTodosCount }}</p>
    <p v-if="filter === 'all'" class="task-count">Total Tasks: {{ todos.length }}</p>
  </div>
</template>

<script>
import { ref, computed } from 'vue';
import { useTodoStore } from './stores/todo';

export default {
  setup() {
    const newTodo = ref('');
    const filter = ref('all');
    const todoStore = useTodoStore();

    const addTodo = () => {
      if (newTodo.value.trim()) {
        todoStore.addTodo(newTodo.value);
        newTodo.value = '';
      }
    };

    const removeTodo = (index) => {
      todoStore.removeTodo(index);
    };

    const toggleTodoStatus = (todo) => {
      const index = todoStore.todos.indexOf(todo);
      todoStore.toggleTodo(index);
    };

    const deleteAll = () => {
      todoStore.clearTodos();
    };

    const filteredTodos = computed(() => {
      if (filter.value === 'unfinished') return todoStore.unfinishedTodos;
      if (filter.value === 'finished') return todoStore.finishedTodos;
      return todoStore.todos;
    });

    return {
      newTodo,
      filter,
      filteredTodos,
      addTodo,
      removeTodo,
      toggleTodoStatus,
      deleteAll,
      todos: todoStore.todos,
      unfinishedTodosCount: computed(() => todoStore.unfinishedTodosCount),
      finishedTodosCount: computed(() => todoStore.finishedTodosCount)
    };
  }
};
</script>

<style>
body {
  background: linear-gradient(120deg, #89f7fe 0%, #66a6ff 100%);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  margin: 0;
  padding: 0;
}

.app {
  max-width: 600px;
  margin: 50px auto;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  background-color: #fff;
}

h1 {
  color: #333;
  text-align: center;
  margin-bottom: 20px;
  font-size: 32px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
}

.input-container {
  display: flex;
  margin-bottom: 20px;
}

input[type="text"] {
  flex: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px 0 0 5px;
}

button {
  padding: 10px 20px;
  border: none;
  border-radius: 0 5px 5px 0;
  background-color: #5cb85c;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #4cae4c;
}

.filter-buttons {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.filter-buttons button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #5cb85c;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin: 0 5px;
}

.filter-buttons button.active {
  background-color: #4cae4c;
}

ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  border-radius: 5px;
  background-color: #f2f2f2;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.todo-item {
  display: flex;
  align-items: center;
}

.todo-item input[type="checkbox"] {
  margin-right: 10px;
}

.completed span {
  text-decoration: line-through;
  color: #888;
}

li button {
  padding: 8px 16px;
  border: none;
  border-radius: 5px;
  background-color: #d9534f;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

li button:hover {
  background-color: #c9302c;
}

.delete-all {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #d9534f;
  color: #fff;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.delete-all:hover {
  background-color: #c9302c;
}

.task-count {
  margin-top: 20px;
  font-size: 1.1em;
  color: #555;
  text-align: center;
}
</style>
