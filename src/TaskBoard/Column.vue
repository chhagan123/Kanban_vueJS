<script setup>
import { onMounted, ref, watch, computed } from "vue";
import Task from "./Task.vue";

const props = defineProps({
  showAddColunm: Boolean,
  tasks: Array,
  searchTerm: String, // 👈 coming from parent (MainBoard)
  searchAssine:String, // also coming from parent (MainBoard)
});

const emit = defineEmits(["openAddTask", "editTask", "delete", "deleteColumn"]);
const columns = ref([]);

// 🔹 Filter tasks per column
function getFilteredTasks(colId) {
  return props.tasks.filter((task) => {
    // must belong to this column
    const matchesColumn = task.columnId === colId;

    // filter by assignee (if set)
    const matchesAssignee = !props.searchAssine
      ? true
      : task.assignee?.toLowerCase() === props.searchAssine.toLowerCase();

    // filter by search text (if set)
    const matchesSearch = !props.searchTerm
      ? true
      : task.title.toLowerCase().includes(props.searchTerm.toLowerCase());

    return matchesColumn && matchesAssignee && matchesSearch;
  });
}


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

// receive new column
function addColumn(newColumn) {
  columns.value.push(newColumn);
}
defineExpose({ addColumn });

const notDeletecol = ["Todo", "Progress", "Done"];

// delete column
function deletecolum(column) {
  if (confirm(`Are you sure you want to delete column "${column.name}"?`)) {
    columns.value = columns.value.filter((c) => c.id !== column.id);
  }
}
</script>

<template>
  <div class="flex gap-6 w-full mt-4 p-4 items-start">
    <div
      v-for="col in columns"
      :key="col.id"
      class="w-60 h-auto border rounded-lg p-2 flex flex-col flex-shrink-0"
    >
      <div class="flex border w-full mb-2 pb-2 justify-start gap-4 items-center">
        <!-- Column name and filtered task count -->
        <h1 class="font-bold ml-2">{{ col.name }}</h1>
        <div
          class="w-6 h-6 rounded-full shadow-lg flex items-center justify-center bg-indigo-600"
        >
          <span class="text-white font-normal text-xs">
            {{ getFilteredTasks(col.id).length }}
          </span>
        </div>

        <!-- Delete button only for non-default columns -->
        <button
          v-if="!notDeletecol.includes(col.name)"
          class="border bg-red-500 text-white px-2 rounded hover:bg-red-600"
          @click="deletecolum(col)"
        >
          Delete
        </button>
      </div>

      <!-- Render filtered tasks for this column -->
      <div class="flex flex-col gap-4 w-full h-full overflow-y-auto">
        <Task
          v-for="(t, index) in getFilteredTasks(col.id)"
          :key="index"
          :task="t"
          @openedit="emit('editTask', $event)"
          @deletetask="emit('delete', $event)"
        />
      </div>

      <!-- Add Task Button -->
      <button
        @click="emit('openAddTask', col.id)"
        class="mt-4 bg-indigo-600 text-white px-2 py-1 rounded text-sm hover:bg-indigo-700 w-auto"
      >
        ➕ Add Task
      </button>
    </div>
  </div>
</template>
