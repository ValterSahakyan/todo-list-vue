<template>
  <div id="app">
    <div class="todo-container">
      <div class="todo-input">
        <input
            v-model="newTodo"
            @keyup.enter="addTodo"
            placeholder="Add a new todo..."
        >
        <button @click="addTodo">Add</button>
      </div>

      <div class="todo-lists">
        <TodoList
            title="Pending"
            :todos="pendingTodos"
            @update="handleUpdate"
            @remove="removeTodo"
            @reorder="handlePendingReorder"
            @move="handleMoveItem"
        />

        <TodoList
            title="Completed"
            :todos="completedTodos"
            @update="handleUpdate"
            @remove="removeTodo"
            @reorder="handleCompletedReorder"
            @move="handleMoveItem"
        />
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import TodoList from './components/TodoList.vue'

export default {
  components: { TodoList },
  setup() {
    const newTodo = ref('')
    const todos = ref([
      { id: 1, text: 'Lorem Ipsum is simply dummy text of the printing and typesetting industry.', completed: false },
      { id: 2, text: 'It is a long established fact that a reader will be distracted by the readable content of a page when looking at its layout.', completed: false },
      { id: 3, text: 'Contrary to popular belief, Lorem Ipsum is not simply random text. It has roots in a piece of classical Latin literature from 45 BC', completed: true }
    ])

    const pendingTodos = computed(() =>
        todos.value.filter(todo => !todo.completed)
    )

    const completedTodos = computed(() =>
        todos.value.filter(todo => todo.completed)
    )

    const addTodo = () => {
      if (newTodo.value.trim() === '') return

      todos.value.push({
        id: Date.now(),
        text: newTodo.value.trim(),
        completed: false
      })

      newTodo.value = ''
    }

    const removeTodo = (id) => {
      todos.value = todos.value.filter(todo => todo.id !== id)
    }

    const handleUpdate = (updatedTodo) => {
      const index = todos.value.findIndex(t => t.id === updatedTodo.id)
      if (index !== -1) {
        todos.value[index] = updatedTodo
      }
    }

    const handlePendingReorder = (newPending) => {
      todos.value = [...newPending, ...completedTodos.value]
    }

    const handleCompletedReorder = (newCompleted) => {
      todos.value = [...pendingTodos.value, ...newCompleted]
    }

    const handleMoveItem = ({ id, completed }) => {
      const index = todos.value.findIndex(t => t.id === id)
      if (index !== -1) {
        todos.value[index].completed = completed
      }
    }

    return {
      newTodo,
      todos,
      pendingTodos,
      completedTodos,
      addTodo,
      removeTodo,
      handleUpdate,
      handlePendingReorder,
      handleCompletedReorder,
      handleMoveItem
    }
  }
}
</script>

<style scoped>
.todo-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.todo-input {
  display: flex;
  margin-bottom: 20px;
}

.todo-input input {
  flex-grow: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px 0 0 4px;
}

.todo-input button {
  padding: 10px 15px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 0 4px 4px 0;
  cursor: pointer;
}

.todo-input button:hover {
  background-color: #369f6b;
}

.todo-lists {
  display: flex;
  gap: 20px;
  min-height: 300px;
}
</style>