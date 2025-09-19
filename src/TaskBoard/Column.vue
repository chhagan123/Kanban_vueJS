<script setup>
import { onMounted, ref, watch } from "vue";
import Task from "./Task.vue";
import draggable from "vuedraggable";

const props = defineProps({
  showAddColunm: Boolean,
  tasks: Array,
  searchTerm: String, // from parent (MainBoard)
  searchAssine: String, // from parent (MainBoard)
});

const emit = defineEmits(["openAddTask", "editTask", "delete", "deleteColumn"]);
const columns = ref([]);

// 🔹 Filter tasks per column
function getFilteredTasks(colId) {
  return props.tasks.filter((task) => {
    const matchesColumn = task.columnId === colId;

    const matchesAssignee = !props.searchAssine
      ? true
      : task.assignee?.toLowerCase() === props.searchAssine.toLowerCase();

    const matchesSearch = !props.searchTerm
      ? true
      : task.title.toLowerCase().includes(props.searchTerm.toLowerCase());

    return matchesColumn && matchesAssignee && matchesSearch;
  });
}

// 🔹 Handle drag end
function onDragEnd(evt) {
  const { item, to } = evt;

  // Find new column id (using data attribute)
  const newColId = to.closest("[data-col-id]")?.dataset.colId;

  if (newColId) {
    // Find dragged task by data-task-id
    const taskId = item.getAttribute("data-task-id");
    const task = props.tasks.find((t) => t.id.toString() === taskId);

    if (task) {
      task.columnId = newColId; // update columnId after drop
    }
  }

  // Save tasks state if you want persistence
  localStorage.setItem("kanban-tasks", JSON.stringify(props.tasks));
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
        columns.value.push(def);
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
      :data-col-id="col.id"
    >
      <div class="flex border w-full mb-2 pb-2 justify-start gap-4 items-center">
        <h1 class="font-bold ml-2">{{ col.name }}</h1>
        <div
          class="w-6 h-6 rounded-full shadow-lg flex items-center justify-center bg-indigo-600"
        >
          <span class="text-white font-normal text-xs">
            {{ getFilteredTasks(col.id).length }}
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

      <!-- 🔹 Draggable Tasks -->
      <draggable
        :list="getFilteredTasks(col.id)"
        :group="{ name: 'tasks', pull: true, put: true }"
        item-key="id"
        class="flex flex-col gap-4 w-full h-full overflow-y-auto"
        @end="onDragEnd"
      >
        <template #item="{ element }">
          <Task
            :task="element"
            :data-task-id="element.id"
            @openedit="emit('editTask', $event)"
            @deletetask="emit('delete', $event)"
          />
        </template>
      </draggable>

      <button
        @click="emit('openAddTask', col.id)"
        class="mt-4 bg-indigo-600 text-white px-2 py-1 rounded text-sm hover:bg-indigo-700 w-auto"
      >
        ➕ Add Task
      </button>
    </div>
  </div>
</template>
