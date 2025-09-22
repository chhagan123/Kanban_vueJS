<script setup>
import Toolbar from "./Toolbar.vue";
import AddTask from "../taskmodel/AddTask.vue";
import Column from "../TaskBoard/Column.vue";
import AddColum from "../taskmodel/AddColum.vue";
import EditTask from "../taskmodel/EditTask.vue";
import { onMounted, ref, watch } from "vue";

const ShowTask = ref(false);
const activeColumn = ref(null); // 👈 track which column is adding task
const showAddColunm = ref(false);
const columnRef = ref(null);
const searchTerm = ref("");
const searchAssine = ref("");

// undo ref

const history = ref([])
// const future = ref([])
// const count = ref(0)

// function handleundo(){
//  const newval  =  tasks.value.pop()
//  future.value.push(newval)
//   count.value=0
 
// }
// console.log(future.value)
// function handleRedo() {
//   tasks.value.push(future.value[count.value])
//   count.value++;
  
// }




//undo & redo 

// function updateTaskboard () {
  
//   history.value = 

// }


/// change theme by toggling
const theme = ref(true);

function changeTheme() {
  theme.value = !theme.value;
}

// search by titile
function handlesearch(query) {
  searchTerm.value = query.toLowerCase();
  // console.log(searchTerm.value);
}

// search by Assignee
function AssigneSearch(query) {
  searchAssine.value = query.toLowerCase();
  console.log(searchAssine.value);
}

const showEdit = ref(false);
const selectedTask = ref(null);

function maintoggle() {
  ShowTask.value = !ShowTask.value;
}

function togglecol() {
  showAddColunm.value = !showAddColunm.value;
}

const tasks = ref([]);
function updateTasks(newTasks) {
  tasks.value = [...newTasks]; // reassign so reactivity triggers
}

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

function updatecol(colId) {
  activeColumn.value = colId;
  ShowTask.value = true;
  // console.log(ShowTask.value);
}

function handleaddTask(newTask) {
  // history.value.push(newTask)
  tasks.value.push(newTask);
  ShowTask.value = false;
  // console.log(ShowTask.value);
}

// handle Addcolum
function handleAddColumn(newCol) {
  columnRef.value.addColumn(newCol);
  showAddColunm.value = false;
}

// handle edit task

function openeditTask(task) {
  selectedTask.value = task;
  showEdit.value = true;
}

// handleUpdate Task

function handleUpdateTask(updateTask) {
  const index = tasks.value.findIndex((t) => t.id === updateTask.id);
  if (index !== -1) {
    tasks.value[index] = { ...updateTask };
  }
  showEdit.value = false;
}

// handleDelete Task

function deleteTask(task) {
  if (confirm(`Are you sure you want to delete "${task.title}"?`)) {
    tasks.value = tasks.value.filter((t) => t.id !== task.id);
  }
}
</script>

<template>
  <div class="flex  flex-col justify-center items-center " >
    <h1 class="mt-[4rem]">Welcome to Kanban Board</h1>
    <Toolbar
    
      @togglecol="togglecol"
      @search="handlesearch"
      @assignee="AssigneSearch"
      @theme = "changeTheme"
      :theme="theme"
    />

    <Column
      ref="columnRef"
      :tasks="tasks"
      :showAddColunm="showAddColunm"
      :searchTerm="searchTerm"
      :theme="theme"
      :searchAssine="searchAssine"
      @openAddTask="updatecol"
      @updateTasks="updateTasks"
      @editTask="openeditTask"
      @delete="deleteTask"
    />

    <AddTask
      v-if="ShowTask"
      :column-id="activeColumn"
      @addTask="handleaddTask"
      @close="maintoggle"
    />
    <AddColum
      v-if="showAddColunm"
      @addColumn="handleAddColumn"
      @close="togglecol"
    />

    <EditTask
      v-if="showEdit"
      v-model="showEdit"
      :task="selectedTask"
      @update-task="handleUpdateTask"
    />
  </div>
</template>
