<script setup>
import { onMounted, ref, watch } from "vue";
import Task from "./Task.vue";
const props = defineProps({ showAddColunm: Boolean, tasks: Array });
const emit = defineEmits(["openAddTask"]);
const columns = ref([]);
onMounted(() => {
  const saved = localStorage.getItem("kanban-col");
  if (saved) {
    columns.value = JSON.parse(saved);
  } else {
    columns.value = [
      { id: "todo", name: "Todo" },
      { id: "progress", name: "Progress" },
      { id: "done", name: "Done" },
    ];
  }
});
watch(
  columns,
  (newval) => {
    localStorage.setItem("kanban-col", JSON.stringify(newval));
  },
  { deep: true }
);


// ricieve new colum 

function addColumn (newColumn) {
  columns.value.push(newColumn)
}

defineExpose({addColumn})

</script>
<template>
  <div class="flex gap-6 w-full h-auto mt-4 p-4">
    <div
      v-for="col in columns"
      :key="col.id"
      class="w-60 h-auto border rounded-lg p-2 flex flex-col items-start"
    >
      <h1 class="font-bold">{{ col.name }}</h1>
      <div class="flex flex-col gap-4 w-full">
        <!-- Render only tasks that match this column -->
        <Task
          v-for="(t, index) in tasks.filter((task) => task.columnId === col.id)"
          :key="index"
          :task="t"
        />
      </div>
      <!-- Button to add task in this column -->
      <button
        @click="emit('openAddTask', col.id)"
        class="mt-4 bg-indigo-600 text-white px-2 py-1 rounded text-sm hover:bg-indigo-700"
      >
        ➕ Add Task
      </button>
    </div>
  </div>
 
</template>
