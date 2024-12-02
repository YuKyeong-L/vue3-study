<template>
  <div class="container">
    <h2>To-Do List</h2>
    <input
      type="text"
      class="form-control"
      placeholder="search"
      v-model="searchText"
    />
    <hr />
    <TodoSimpleForm @add-todo="addTodo" />
    <div style="color: red">{{ error }}</div>

    <div v-if="!filteredTodos.length">There is nothing to display</div>

    <TodoList
      :todos="filteredTodos"
      @toggle-todo="toggleTodo"
      @delete-todo="deleteTodo"
    />
  </div>
</template>

<script>
import { ref, computed } from "vue";
import TodoSimpleForm from "./components/TodoSimpleForm.vue";
import TodoList from "./components/TodoList.vue";
import axios from "axios";

export default {
  components: {
    TodoSimpleForm,
    TodoList,
  },
  setup() {
    const todos = ref([]);
    const error = ref("");

    const getTodos = async () => {
      try {
        const res = await axios.get("http://localhost:3000/todos");
        todos.value = res.data;
      } catch (err) {
        error.value = "Something went wrong.";
      }
    };

    getTodos();

    const addTodo = async (todo) => {
      // 데이터 베이스에 todo를 저장
      error.value = "";
      try {
        const res = await axios.post("http://localhost:3000/todos", {
          subject: todo.subject,
          completed: todo.completed,
        });
        todos.value.push(res.data);
      } catch (err) {
        error.value = "Something went wrong.";
      }

      // 마지막에 작성되었지만 앞의 데이터가 받아지는 동안 먼저 실행됨
      // console.log("Hello");
    };

    const toggleTodo = async (index) => {
      error.value = "";
      const id = todos.value[index].id;
      try {
        await axios.patch("http://localhost:3000/todos/" + id, {
          completed: !todos.value[index].completed,
        });
        todos.value[index].completed = !todos.value[index].completed;
      } catch (err) {
        console.log(err);
        error.value = "Something went wrong.";
      }
    };

    const deleteTodo = async (index) => {
      error.value = "";
      const id = todos.value[index].id;
      try {
        await axios.delete("http://localhost:3000/todos/" + id);
        todos.value.splice(index, 1);
      } catch (err) {
        console.log(err);
        error.value = "Something went wrong.";
      }
    };

    const searchText = ref("");
    const filteredTodos = computed(() => {
      if (searchText.value) {
        // 검색값이 있으면 (필터된)그것을 반환
        return todos.value.filter((todo) => {
          return todo.subject.includes(searchText.value);
        });
      }

      // 검색값이 없으면 전체를 반환
      return todos.value;
    });

    return {
      todos,
      addTodo,
      // todoStyle,
      deleteTodo,
      toggleTodo,
      searchText,
      filteredTodos,
      error,
      getTodos,
    };
  },
};
</script>

<style>
.todo {
  color: gray;
  text-decoration: line-through;
}
</style>