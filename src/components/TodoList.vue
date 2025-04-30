<template>
  <div class="todo-list" :class="title.toLowerCase()" ref="listContainer">
    <h2>{{ title }}</h2>
    <div class="todo-items-container" ref="itemsContainer">
      <div
          v-for="todo in todos"
          :key="todo.id"
          class="todo-item"
          :data-id="todo.id"
      >
        <input
            type="checkbox"
            v-model="todo.completed"
            @change="$emit('update', todo)"
        >
        <span :class="{completed: todo.completed}">{{ todo.text }}</span>
        <a href="#" @click.prevent="$emit('remove', todo.id)" class="remove">Remove</a>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import Sortable from 'sortablejs'

export default {
  props: {
    title: String,
    todos: Array
  },
  emits: ['update', 'remove', 'reorder', 'move'],
  setup(props, { emit }) {
    const itemsContainer = ref(null)

    onMounted(() => {
      const sortableInstance = new Sortable(itemsContainer.value, {
        group: {
          name: 'todos',
          pull: true,
          put: true
        },
        animation: 150,
        ghostClass: 'sortable-ghost',
        onEnd: (evt) => {
          const itemId = evt.item.dataset.id
          const targetListClass = evt.to.closest('.todo-list').classList
          const isCompleted = targetListClass.contains('completed')

          emit('move', {
            id: parseInt(itemId),
            completed: isCompleted
          })

          if (evt.from === evt.to) {
            const items = [...props.todos]
            const [movedItem] = items.splice(evt.oldIndex, 1)
            items.splice(evt.newIndex, 0, movedItem)
            emit('reorder', items)
          }
        }
      })
    })

    return {
      itemsContainer
    }
  }
}
</script>

<style scoped>
.todo-list {
  flex: 1;
  padding: 15px;
  background-color: #f5f5f5;
  border-radius: 4px;
}

.todo-list h2 {
  margin-top: 0;
  padding-bottom: 10px;
  border-bottom: 1px solid #ddd;
}

.todo-items-container {
  min-height: 100px;
  padding: 10px;
}

.todo-item {
  display: flex;
  align-items: center;
  padding: 10px;
  margin-bottom: 8px;
  background-color: white;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
  cursor: move;
}

.todo-item input[type="checkbox"] {
  margin-right: 10px;
}

.todo-item span {
  flex-grow: 1;
}

.todo-item span.completed {
  text-decoration: line-through;
  color: #888;
}

.todo-item .remove {
  color: #ff4444;
  text-decoration: none;
  margin-left: 10px;
}

.todo-item .remove:hover {
  text-decoration: underline;
}

.sortable-ghost {
  opacity: 0.5;
  background: #c8ebfb;
}
</style>