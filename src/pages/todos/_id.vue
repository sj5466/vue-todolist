<template>
  <h1>Todo Page</h1>
  <div v-if="loading">Loading...</div>
  <form v-else>
    <div class="form-group">
      <label for="">Todo Subject</label>
      <input v-model="todo.subject" type="text" class="form-control" />
    </div>
    <button class="btn btn-primary">저장</button>
  </form>
</template>

<script>
import { useRoute } from "vue-router";
import { ref } from "@vue/reactivity";
import axios from "axios";

export default {
  setup() {
    const route = useRoute();
    const todo = ref(null);
    const loading = ref(true);

    const getTodo = async () => {
      const res = await axios.get(
        "http://localhost:3000/todos/" + route.params.id
      );

      todo.value = res.data;
      loading.value = false;
      console.log(res);
    };
    console.log(route.params.id);

    getTodo();

    return {
      todo,
      loading,
    };
  },
};
</script>

<style></style>
