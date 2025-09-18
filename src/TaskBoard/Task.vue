<script setup>
import { ref } from 'vue';

defineProps({
  task: {
    type: Object,
    required: true
  }
})

</script>

<template>
 <div class="p-4 bg-gray-500 text-white rounded-lg shadow">
    <h3 class="text-lg font-bold">{{ task.title }}</h3>
    <p class="text-sm text-gray-300">{{ task.description }}</p>
    <p class="text-sm mt-1">👤 {{ task.Assignees }}</p>
    <p class="text-sm mt-1">📅 {{ task.date }}</p>
  </div>
</template>