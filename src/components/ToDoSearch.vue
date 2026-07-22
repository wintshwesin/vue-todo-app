<template>
  <input type="text" class="form-control" :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
    placeholder="タスク名で検索"
  />
</template>

<script setup>
  defineProps({
    modelValue: String
  });

  defineEmits(["update:modelValue"]);
</script>