<template>
  <div class="todo-item">
    <input
        type="checkbox"
        v-model="todo.completed"
        @change="handleChange"
    >
    <span :class="{completed: todo.completed}">{{ todo.text }}</span>
    <a href="#" @click.prevent="handleRemove" class="remove">Remove</a>
  </div>
</template>

<script>
export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  emits: ['update', 'remove'],
  methods: {
    handleChange() {
      this.$emit('update', {...this.todo})
    },
    handleRemove() {
      this.$emit('remove', this.todo.id)
    }
  }
}
</script>

<style scoped>
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
</style>