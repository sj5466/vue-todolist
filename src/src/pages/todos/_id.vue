<template>
  <h1>To-Do Page</h1>
  <div v-if="loading">Loading...</div>
  <form v-else>
    <div class="row">
      <div class="col-6">
        <div class="form-group">
          <label for="">Subject</label>
          <input v-model="todo.subject" type="text" class="form-control" />
        </div>
      </div>
      <div class="col-6">
        <div class="form-group">
          <label for="">Status</label>
          <div>
            <button
              class="btn"
              type="button"
              @click="toggleTodoStatus()"
              :class="todo.completed ? 'btn-success' : 'btn-danger'"
            >
              {{ todo.completed ? "Completed" : "Incomplete" }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <button type="submit" class="btn btn-primary">Save</button>
    <button class="btn btn-outline-dark ml-2" @click="moveTodoListPage()">
      Cancle
    </button>
  </form>
</template>

<script>
import { useRoute, useRouter } from "vue-router";
import { ref } from "@vue/reactivity";
import axios from "axios";

export default {
  setup() {
    const route = useRoute();
    const todo = ref(null);
    const router = useRouter();
    const loading = ref(true);

    const getTodo = async () => {
      const res = await axios.get(
        "http://localhost:3000/todos/" + route.params.id
      );

      todo.value = res.data;
      loading.value = false;
    };

    const toggleTodoStatus = () => {
      todo.value.completed = !todo.value.completed;
    };

    const moveTodoListPage = () => {
      router.push({
        name: "Todos",
      });
    };
    getTodo();
    return {
      todo,
      loading,
      toggleTodoStatus,
      moveTodoListPage,
    };
  },
};
</script>

<style></style>
