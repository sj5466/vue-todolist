<template>
  <div>
    <h2>To-Do-List</h2>
    <input
      type="text"
      class="form-control"
      v-model="searchText"
      placeholder="Search"
    />
    <hr />
    <TodoSimpleFormVue @add-todo="addTodo()" />
    <div style="color: red">{{ error }}</div>
    <div v-if="!todos.length">There is nothing to display</div>
    <TodoListVue
      :todos="todos"
      @toggle-todo="toggleTodo"
      @delete-todo="deleteTodo"
    />
    <hr />
    <nav aria-label="Page navigation example">
      <ul class="pagination">
        <li v-if="currentPage !== 1" class="page-item">
          <a
            style="cursor: pointer"
            class="page-link"
            @click="getTodos(currentPage - 1)"
            >Previous</a
          >
        </li>
        <li
          class="page-item"
          v-for="page in numberOfPages"
          :key="page"
          :class="currentPage === page ? 'active' : ''"
        >
          <a
            style="cursor: pointer"
            class="page-link"
            @click="getTodos(page)"
            >{{ page }}</a
          >
        </li>
        <li v-if="numberOfPages !== currentPage" class="page-item">
          <a
            style="cursor: pointer"
            class="page-link"
            @click="getTodos(currentPage + 1)"
            >Next</a
          >
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import { ref, computed, watch } from "vue";
import TodoSimpleFormVue from "@/components/TodoSimpleForm.vue";
import TodoListVue from "@/components/TodoList.vue";
import axios from "axios";

export default {
  components: {
    TodoSimpleFormVue,
    TodoListVue,
  },
  setup() {
    const todoStyle = {
      //checkbox로 check된 todo에만 해당 style 지정
      textDecoration: "line-through",
      color: "gray",
    };

    const todos = ref([]); //검색창의 입렵된 값(todo)을 todos 객체 안에 담김
    const error = ref("");
    const numberOfTodos = ref(0);
    const limit = 5;
    const currentPage = ref(1);
    const searchText = ref("");

    const numberOfPages = computed(() => {
      return Math.ceil(numberOfTodos.value / limit);
    });

    //default 값으로 page값에 currentPage.value를 넣어줌
    const getTodos = async (page = currentPage.value) => {
      currentPage.value = page;
      try {
        const res = await axios.get(
          `http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}`
        );
        //api 호출 할 때 _sort /_order 를 이용해 정렬을 수정함 새로 추가한 todo가 처음에 뜨도록 수정됨
        console.log(res);
        numberOfTodos.value = res.headers["x-total-count"];
        todos.value = res.data;
      } catch (err) {
        console.log(err);
        error.value = "Something went wrong";
      }
    };
    getTodos();

    const addTodo = async (todo) => {
      error.value = "";
      try {
        await axios.post("http://localhost:3000/todos", {
          subject: todo.subject,
          completed: todo.completed,
        });
        //새로운 todo가 추가되면 getTodos메서드가 실행되고 첫번페이지가 불러와 지기 때문에 새로 추가한 todo는 맨 마지막에 추가됨
        getTodos(1);
      } catch (err) {
        console.log(err);
        error.value = "Something went wrong";
      }
    };

    const deleteTodo = async (index) => {
      error.value = "";
      const id = todos.value[index].id;
      try {
        await axios.delete("http://localhost:3000/todos/" + id);
        getTodos(1); // todo가 삭제되면 첫번째 페이지가 보이도록 설정
      } catch (err) {
        console.log(err);
        err.value = "Something went wrong";
      }
    };

    const toggleTodo = async (index, checked) => {
      console.log(checked);
      error.value = "";
      const id = todos.value[index].id;
      try {
        await axios.patch("http://localhost:3000/todos/" + id, {
          // completed: !todos.value[index].completed,
          completed: checked,
        });
        // todos.value[index].completed = !todos.value[index].completed;
        todos.value[index].completed = checked;
      } catch (err) {
        console.log(err);
        err.value = "Something went wrong";
      }
    };

    /**그 전 검색 기능 현재 있는 페이지의 검색만을 인식했다면
     * watch를 사용하면 모든 페이지를 인식하고 해당 페이지로 이동도 할 수 있게됨
     */
    watch(searchText, () => {
      getTodos(1); // getTodos 에 인자 값으로 1값을 넘기으로서 검색도니 페이지는 1페이지로 보이게 됨
    });

    // watch(searchText, (current, prev) => {
    //   //current -> 최근 검색된 값
    //   //prev -> 이전 값 인식
    //   console.log(current, prev);
    // });
    // const filterTodos = computed(() => {
    //   if (searchText.value) {
    //     return todos.value.filter((todo) => {
    //       //todo.subject 안에 searchText.value 값이 포함이 되어 있을 때만 화면에 보이도록 filter
    //       return todo.subject.includes(searchText.value);
    //     });
    //   }
    //   //searchText.value에 값이 들어오면 todos.value로 값을 할당
    //   return todos.value;
    // });

    return {
      todos,
      addTodo,
      todoStyle,
      deleteTodo,
      toggleTodo,
      searchText,
      // filterTodos,
      error,
      getTodos,
      numberOfPages,
      currentPage,
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
