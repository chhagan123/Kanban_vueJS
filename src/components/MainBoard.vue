<script setup>
import Toolbar from "./Toolbar.vue";
import AddTask from "../taskmodel/AddTask.vue";
import Column from "../TaskBoard/Column.vue";
import AddColum from "../taskmodel/AddColum.vue";
import { onMounted, ref, watch } from "vue";

const ShowTask = ref(false);
const activeColumn = ref(null); // 👈 track which column is adding task
const showAddColunm = ref(false);
const columnRef = ref(null);

function maintoggle() {
  ShowTask.value = !ShowTask.value;
}

function togglecol() {
  showAddColunm.value = !showAddColunm.value;
}

const tasks = ref([]);

// onmounted task

onMounted(() => {
  const saved = localStorage.getItem("Kanban-task");
  if (saved) {
    tasks.value = JSON.parse(saved);
  }
});

// watch if task added to localsorage to the set

watch(
  tasks,
  (newTask) => {
    localStorage.setItem("Kanban-task", JSON.stringify(newTask));
  },
  { deep: true }
);

// Catch emitted task
function handleaddTask(newTask) {
  tasks.value.push(newTask);
  ShowTask.value= false;
}

// handle Addcolum
function handleAddColumn(newCol) {
  columnRef.value.addColumn(newCol);
  showAddColunm.value = false;
}

</script>

<template>
  <div class="flex flex-col justify-center items-center">
    <h1 class="mt-[4rem]">Welcome to Kanban Board</h1>
    <Toolbar @togglecol="togglecol" />

    <Column
       ref="columnRef"
      :tasks="tasks"
      :showAddColunm="showAddColunm"
      @openAddTask="
        (colId) => {
          activeColumn = colId;
          ShowTask = true;
        }
      "
    />

    <AddTask
      v-if="ShowTask"
      :column-id="activeColumn"
      @addTask="handleaddTask"
      @close="maintoggle"
    />
    <AddColum v-if="showAddColunm"
      @addColumn="handleAddColumn"
      @close="togglecol"/>
  </div>
</template>

