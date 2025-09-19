<script setup>
import { onMounted, ref, watch } from "vue";
import Task from "./Task.vue";
const props = defineProps({ showAddColunm: Boolean, tasks: Array });
const emit = defineEmits(["openAddTask","editTask","delete","deleteColumn"]);
const columns = ref([]);
onMounted(() => {
  const saved = localStorage.getItem("kanban-col");
  if (saved) {
    columns.value = JSON.parse(saved);

    // ensure default columns always exist
    const defaults = [
      { id: "todo", name: "Todo" },
      { id: "progress", name: "Progress" },
      { id: "done", name: "Done" },
    ];

    defaults.forEach(def => {
      if (!columns.value.some(c => c.id === def.id)) {
        columns.value.push(def); // ✅ add back missing default
      }
    });
  } else {
    // if nothing in storage, just set defaults
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

const notDeletecol = ["Todo","Progress","Done"]

// deletecolum 

function deletecolum(column) {
  if (confirm(`Are you sure you want to delete column "${column.name}"?`)) {
    columns.value = columns.value.filter(c => c.id !== column.id);
    // emit("deleteColumn", column); // optional: notify parent
  }
}

</script>
<template>
  <div class="flex gap-6 w-full h-auto mt-4 p-4">
    <div
      v-for="col in columns"
      :key="col.id"
      class="w-60 h-auto border rounded-lg p-2 flex flex-col items-start"
    >
     <div class="flex border w-full mb-2 gap-4">
      <h1 class="font-bold">{{ col.name }}</h1>
      <button
      v-if="!notDeletecol.includes(col.name)"
      class="border bg-red-500 text-white px-2 rounded"
      @click="deletecolum(col)"
    >
      Delete
    </button>
     </div>
     
      <div class="flex flex-col gap-4 w-full">
        <!-- Render only tasks that match this column -->
        <Task
          v-for="(t, index) in tasks.filter((task) => task.columnId === col.id)"
          :key="index"
          :task="t"
          @openedit="emit('editTask', $event)" 
          @deletetask="emit('delete', $event)"
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