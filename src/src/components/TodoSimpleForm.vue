<template>
  <form @submit.prevent="onSubmit">
    <div class="d-flex">
      <div class="flex-grow-1 mr-2">
        <input
          type="text"
          class="form-control"
          v-model="todo"
          placeholder="Type new to-do-list"
        />
      </div>
      <div>
        <button class="btn btn-primary" type="submit">Add</button>
      </div>
    </div>
    <div v-show="hasError" style="color: red">This field cannot be empty</div>
  </form>
</template>

<script>
import { ref } from "vue";
export default {
  emits: ["add-todo"],
  setup(props, { emit }) {
    const todo = ref(""); //검색창의 입력된 값
    const hasError = ref(false);
    const onSubmit = () => {
      if (todo.value === "") {
        //검색창에 아무것도 입려되지 않을 때 경고 문구가 뜨도록 설정
        hasError.value = true;
      } else {
        //부모컴퍼넌트에 해당 데이터를 올려준다.
        emit("add-todo", {
          id: Date.now(),
          subject: todo.value,
          completed: false,
        });
        //값이 들어 왔기에 hasError가 발생되지 않도록 false
        hasError.value = false;
        //todo값 초기화 -> 초기화를 시키지 않으면 값이 todos 객체에 담겨도 검색창에 검색어가 남아있음
        todo.value = "";
      }
    };

    return {
      todo,
      hasError,
      onSubmit,
    };
  },
};
</script>
<style>
.mr-2 {
  margin-right: 20px;
}
</style>
