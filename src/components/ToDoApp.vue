<script setup>
import ToDoInput from './ToDoInput.vue';
import ToDoListView from './ToDoListView.vue';
import {ref} from "vue";
import {statuses} from "../const/status";

</script>
<template>
  <div class="todoApp">
    <ToDoInput></ToDoInput>
    <ToDoListView></ToDoListView>
  </div>
</template>