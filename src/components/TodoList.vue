<template>
  <!-- v-for를 쓸 경우 :key를 필수로 추가 -->
  <div class="card mt-2" v-for="(todo, index) in todos" :key="todo.id">
    <div class="card-body d-flex align-items-center p-2">
      <div class="form-check flex-grow-1">
        <!-- v-model="todo.completed" -->
        <!-- 자식 컴포넌트에서 부모의 props를 변경할 수는 없음 -->
        <!-- 따라서 자식 컴포넌트에서 양방향 바인딩인 model를 사용하여 직접 변경x -->
        <!-- 값을 업데이트 하기 위해 bind와 on작업으로 분리하여 실행 -->
        <input
          type="checkbox"
          class="form-check-input"
          :checked="todo.completed"
          @change="toggleTodo(index)"
        />
        <!-- todo.completed의 boolean 값으로 적용 -->
        <label class="form-check-label" :class="{ todo: todo.completed }">
          {{ todo.subject }}
        </label>
      </div>
      <div>
        <button
          type="button"
          class="btn btn-danger btn-sm"
          @click="deleteTodo(index)"
        >
          Delete
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  // :AAA = > props: ["AAA"]
  props: {
    todos: {
      type: Array,
      required: true,
    },
  },
  emits: ["toggle-todo", "delete-todo"],
  setup(props, { emit }) {
    const toggleTodo = (index) => {
      emit("toggle-todo", index);
    };

    const deleteTodo = (index) => {
      emit("delete-todo", index);
    };

    return {
      toggleTodo,
      deleteTodo,
    };
  },
};
</script>

<style>
</style>