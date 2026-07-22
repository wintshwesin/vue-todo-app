<template>
  <div class="container mt-4">
    <h2 class="mb-3">ToDoリスト</h2>

    <table v-if="items.length > 0"　class="table table-striped table-bordered table-hover align-middle text-center">
      <thead class="table-dark">
        <tr>
          <th @click="sortById" style="cursor:pointer"> ID ▲▼</th>
          <th>やること</th>
          <th @click="sortByDate" style="cursor:pointer">期間 ▲▼</th>
          <th>状態</th>
          <th>編集</th>
          <th>削除</th>
        </tr>
      </thead>

      <tbody>
        <tr v-for="item in items" :key="item.id">
          <td>{{ item.id }}</td>
          <td>
            <span v-if="!item.onEdit">{{ item.content }}</span>
            <input class="form-control" v-else type="text" v-model="item.content" />
          </td>
          <td>
            <span v-if="!item.onEdit">{{ item.limit }}</span>
            <input class="form-control" v-else type="date" v-model="item.limit" />
          </td>
          <td>
            <span v-if="!item.onEdit">{{ statuses[item.state].value }}</span>
            <select v-else v-model="item.state" class="form-select">
              <option v-for="(state, key) in statuses" :key="state.id" :value="key">{{ state.value }}</option>
            </select>
          </td>
          <td>
            <button class="btn btn-primary btn-sm" @click="onEdit(item)">
              {{ item.onEdit ? "更新" : "編集" }}
            </button>
          </td>
          <td>
            <button class="btn btn-danger btn-sm" @click="showDeleteModal(item.id)">
              削除
            </button>
          </td>
        </tr>
      </tbody>
    </table>
    <!-- データがない場合 -->
    <div v-else class="alert alert-info text-center">
      リストデータがありません。
    </div>
  </div>
  <div class="modal fade" id="deleteModal" tabindex="-1" ref="deleteModal">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">確認</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
      </div>
      <div class="modal-body">
        {{ deleteItemData?.content }}を削除してもよろしいですか？
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">
          キャンセル
        </button>
        <button type="button" class="btn btn-danger" @click="deleteItem">
          はい
        </button>
      </div>
    </div>
  </div>
</div>
</template>

<script setup>

  import { ref, onMounted, onUnmounted } from "vue";
  import { statuses } from "@/const/status";
  import { Modal } from "bootstrap";

  const items = ref([]);
  const deleteItemData = ref(null);
  const deleteModal = ref(null);

  let modal = null;

  function loadItems() {
    items.value = JSON.parse(localStorage.getItem("items")) || []; 
  }

  function saveItems() {
    localStorage.setItem("items", JSON.stringify(items.value));
    window.dispatchEvent(new Event("todo-updated"));
  }

  onMounted(() => {
    loadItems();
    modal = new Modal(deleteModal.value);
    window.addEventListener("todo-updated", loadItems);
  });

  onUnmounted(() => {
    window.removeEventListener("todo-updated", loadItems);
  });

  const idAsc = ref(true);
  const dateAsc = ref(true);

  function sortById() {
    items.value.sort((a, b) =>
      idAsc.value ? a.id - b.id : b.id - a.id
    );

    idAsc.value = !idAsc.value;
  }

  function sortByDate() {
    items.value.sort((a, b) =>
      dateAsc.value
        ? new Date(a.limit) - new Date(b.limit)
        : new Date(b.limit) - new Date(a.limit)
    );

    dateAsc.value = !dateAsc.value;
  }

  function onEdit(item) {
    if (!item.onEdit) {
      item.onEdit = true;
      return;
    }
    item.onEdit = false;
    saveItems();
  }

  function showDeleteModal(id) {
    deleteItemData.value = item.value.find(item => item.id === id);
    modal.show();
  }

  function deleteItem() {
    items.value = items.value.filter(
      item => item.id !== deleteItemData.value.id
    );

    saveItems();

    deleteItemData.value = null;

    modal.hide();
  }

</script>