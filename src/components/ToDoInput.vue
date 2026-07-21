<template>
  <div class="container mt-5">
    <div
  v-if="isErrorMsg"
  class="alert alert-danger"
  role="alert"
>
  やるタスク・期間の両方を入力してください。
</div>
    <div class="card shadow">
      <div class="card-header bg-primary text-white">
        <h3 class="mb-0">ToDo App</h3>
      </div>

      <div class="card-body">
        <form @submit.prevent="onSubmitForm">

          <div class="mb-3">
            <label class="form-label">やること</label>
            <input
              type="text"
              class="form-control"
              v-model="input"
              placeholder="やることを入力してください"
            />
          </div>

          <div class="mb-3">
            <label class="form-label">期間</label>
            <input
              type="date"
              class="form-control"
              v-model="inputDate"
            />
          </div>

          <button class="btn btn-primary w-100">
            登録
          </button>

        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { onMounted, onUnmounted } from "vue";
import { statuses } from "@/const/status";

const input = ref("");
const inputDate = ref("");

const status = {
  NOT_START: "NOT_START",
};

const items = ref([]);

function loadItems() {
    items.value = JSON.parse(localStorage.getItem("items")) || [];
}

onMounted(() => {
    loadItems();
    window.addEventListener("todo-updated", loadItems);
});

onUnmounted(() => {
    window.removeEventListener("todo-updated", loadItems);
});

const isErrorMsg = ref(false);

function onSubmitForm() {
  if (input.value === "" || inputDate.value === "") {
    isErrorMsg.value = true;
    return;
  }

  isErrorMsg.value = false;

  const items = JSON.parse(localStorage.getItem("items")) || [];

  const newItem = {
    id: items.length + 1,
    content: input.value,
    limit: inputDate.value,
    state: status.NOT_START,
    onEdit: false,
  };

  items.push(newItem);

  localStorage.setItem("items", JSON.stringify(items));

  console.log(localStorage.getItem("items"));

  // Clear the form
  input.value = "";
  inputDate.value = "";
}
</script>