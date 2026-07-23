<template>
  <div class="container mt-5">
    <div v-if="isErrorMsg" class="alert alert-danger" role="alert">
      タスク名と期限を入力してください。
    </div>
    <div class="card shadow">
      <div class="card-header bg-primary text-white">
        <h3 class="mb-0">📋 ToDoタスク管理アプリ</h3>
          <p class="mb-0 mt-2 small">
            タスクの登録・編集・削除・検索ができます。
          </p>
      </div>
      <div class="card-body">
        <form @submit.prevent="onSubmitForm">
          <div class="mb-3">
            <label class="form-label fw-bold">タスク名</label>
            <input type="text" class="form-control" v-model="input" placeholder="例：Vue.jsの勉強" />
          </div>
          <div class="mb-3">
            <label class="form-label fw-bold">期限</label>
            <input type="date" class="form-control" v-model="inputDate" />
          </div>
          <button class="btn btn-primary w-100">＋ タスクを登録</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>

  import { ref } from "vue";

  const input = ref("");
  const inputDate = ref("");
  const isErrorMsg = ref(false);

  const status = {
    NOT_START: "NOT_START",
  };
  
  function getItems() {
    return JSON.parse(localStorage.getItem("items")) || [];
  }

  function saveItems(items) {
    localStorage.setItem("items", JSON.stringify(items));
    window.dispatchEvent(
      new Event("todo-updated")
    );
  }

  function onSubmitForm() {
    if (!input.value || !inputDate.value) {
      isErrorMsg.value = true;
      return;
    }

    isErrorMsg.value = false;

    const items = getItems();

    const newItem = {
      id: items.length > 0 ? Math.max(...items.map(item => item.id)) + 1 : 1,
      content: input.value,
      limit: inputDate.value,
      state: status.NOT_START,
      onEdit: false,
    };

    // 新しいタスクを配列に追加する
    items.push(newItem);

    // localStorageに入力データを保存
    saveItems(items);

    // 入力値を消す
    input.value = "";
    inputDate.value = "";
  }

</script>