<script setup>
import { onMounted, ref, watch } from "vue";
import Task from "./Task.vue";
const props = defineProps({ showAddColunm: Boolean, tasks: Array });
const emit = defineEmits(["openAddTask", "editTask", "delete", "deleteColumn"]);
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

    defaults.forEach((def) => {
      if (!columns.value.some((c) => c.id === def.id)) {
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

function addColumn(newColumn) {
  columns.value.push(newColumn);
}

defineExpose({ addColumn });

const notDeletecol = ["Todo", "Progress", "Done"];

// deletecolum

function deletecolum(column) {
  if (confirm(`Are you sure you want to delete column "${column.name}"?`)) {
    columns.value = columns.value.filter((c) => c.id !== column.id);
    // emit("deleteColumn", column); // optional: notify parent
  }
}
</script>
<template>
  <div  class="flex  gap-6 w-full mt-4 p-4 items-start">
    <div
      v-for="col in columns"
      :key="col.id"
      class="w-60 h-auto border rounded-lg p-2 flex flex-col flex-shrink-0"
    >
      <div
        class="flex border w-full mb-2 pb-2 justify-start gap-4 items-center"
      >
        <!-- Display the column name and task count -->

        <h1 class="font-bold ml-2">{{ col.name }}</h1>
        <div
          class="w-6 h-6 rounded-full shadow-lg flex items-center justify-center bg-indigo-600"
        >
          <span class="text-white  font-normal text-xs">
            {{ tasks.filter((task) => task.columnId === col.id).length }}
          </span>
        </div>

        <button
          v-if="!notDeletecol.includes(col.name)"
          class="border bg-red-500 text-white px-2 rounded hover:bg-red-600"
          @click="deletecolum(col)"
        >
          Delete
        </button>
      </div>

      <!-- Container for tasks with dynamic height -->
      <div class="flex flex-col gap-4 w-full h-full overflow-y-auto">
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
        class="mt-4 bg-indigo-600 text-white px-2 py-1 rounded text-sm hover:bg-indigo-700 w-auto"
      >
        ➕ Add Task
      </button>
    </div>
  </div>
</template>
