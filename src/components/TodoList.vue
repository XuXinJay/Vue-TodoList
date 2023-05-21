<template>
  <div
    class="bg-[#F5F5F5] dark:bg-slate-800 w-[100%] min-h-[100vh] flex flex-col items-center justify-center"
  >
    <div class="flex">
      <h1 class="text-[2rem] dark:text-[#F5F5F5]">待辦清單</h1>
      <button @click="toggleDark()" class="ml-[0.5rem]">
        <i class="uil uil-brightness-half text-[2rem] dark:text-[#F5F5F5]"></i>
      </button>
    </div>

    <div
      class="w-[50%] dark:bg-slate-700 max-w-[400px] min-w-[320px] grid justify-items-center justify-center p-4 rounded-[30px] shadow-md shadow-gray-500/30"
    >
      <div class="w-[100%] flex justify-center">
        <form class="w-[320px] flex justify-center" @submit.prevent="addTodo">
          <input
            v-model="newTodo"
            class="border-2 border-black mr-2 rounded-lg w-[80%] pl-2"
          />
          <button class="bg-[#F7D94C] rounded-lg p-1">
            <i class="uil uil-plus"></i>
          </button>
        </form>
      </div>
      <div
        class="w-[100%] flex justify-center mt-3 rounded-2xl p-2 min-h-[450px]"
      >
        <ul class="w-[100%]">
          <li
            v-for="(todo, index) in todos"
            class="w-[100%] flex decoration-[3px] dark:text-[#F5F5F5]"
            :class="{ 'line-through': todo.isChecked }"
          >
            <div
              v-if="!edit[todo.id]"
              class="w-[100%] flex justify-between mb-1"
            >
              <span
                class="w-[70%] border-b-2 border-[#BDC0BA] dark:text-[#F5F5F5]"
                >{{ todo.text }}</span
              >
              <div>
                <button
                  class="bg-[#F7D94C] rounded-lg p-1 ml-1 text-[black]"
                  @click="toggleChecked(todo)"
                >
                  <i class="uil uil-check-circle"></i>
                </button>
                <button
                  class="bg-[#F7D94C] rounded-lg p-1 ml-1 text-[black]"
                  @click="editTodo(todo)"
                >
                  <i class="uil uil-edit"></i>
                </button>
                <button
                  class="bg-[#F7D94C] rounded-lg p-1 ml-1 text-[black]"
                  @click="removeTodo(todo)"
                >
                  <i class="uil uil-trash-alt"></i>
                </button>
              </div>
            </div>
            <div v-else>
              <input
                class="w-[270px] border-2 border-black mr-2 rounded-lg p-1 mb-1 mt-1 text-[black]"
                v-model="editText[todo.id]"
              />
              <button
                class="bg-[#F7D94C] rounded-lg p-1 text-[black]"
                @click="doneTodo(todo)"
              >
                <i class="uil uil-check"></i>
              </button>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useDark, useToggle } from "@vueuse/core";

let id = 0;
const newTodo = ref("");
const todos = ref([]);
const edit = ref({});
const editText = ref({});
const isDark = useDark();
const toggleDark = useToggle(isDark);
todos.value = JSON.parse(localStorage.getItem("todos") || "[]");

const addTodo = () => {
  todos.value.push({ id: id++, text: newTodo.value, isChecked: false });
  newTodo.value = "";
  saveTodosToLocalStorage();
};

const removeTodo = (todo) => {
  const index = todos.value.indexOf(todo);
  todos.value.splice(index, 1);
  saveTodosToLocalStorage();
};

const editTodo = (todo) => {
  edit.value[todo.id] = true;
  editText.value[todo.id] = todo.text;
  saveTodosToLocalStorage();
};

const doneTodo = (todo) => {
  edit.value[todo.id] = false;
  todo.text = editText.value[todo.id];
  saveTodosToLocalStorage();
};

const saveTodosToLocalStorage = () => {
  localStorage.setItem("todos", JSON.stringify(todos.value));
};

const toggleChecked = (todo) => {
  todo.isChecked = !todo.isChecked;
};
</script>
