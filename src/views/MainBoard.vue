<script setup>
import Toolbar from "./Toolbar.vue"
import AddTask from "../components/taskmodel/AddTask.vue"
import AddColumn from "../components/taskmodel/AddColum.vue"
import EditTask from "../components/taskmodel/EditTask.vue"
import Column from "../components/TaskBoard/Column.vue"
import { onMounted, ref, watch } from "vue"

// State
const showTask = ref(false)
const activeColumnId = ref(null)
const showAddColumn = ref(false)
const columnRef = ref(null)
const searchTerm = ref("")
const searchAssignee = ref("")
const showEdit = ref(false)
const selectedTask = ref(null)
const tasks = ref([])
const theme = ref(true)



// -------------------- Functions --------------------

// Theme toggle
function toggleTheme() {
  theme.value = !theme.value
}

// Search
function handleSearch(query) {
  searchTerm.value = query.toLowerCase()
}

function handleAssigneeSearch(query) {
  searchAssignee.value = query.toLowerCase()
}

// Toggle modals
function toggleTaskModal() {
  showTask.value = !showTask.value
}

function toggleColumnModal() {
  showAddColumn.value = !showAddColumn.value
}

// Task updates
function updateTasks(newTasks) {
  tasks.value = [...newTasks] // trigger reactivity
}

function openAddTask(columnId) {
  activeColumnId.value = columnId
  showTask.value = true
}

function handleAddTask(newTask) {
  tasks.value.push(newTask)
  showTask.value = false
}

function handleEditTask(task) {
  selectedTask.value = task
  showEdit.value = true
}

function handleUpdateTask(updatedTask) {
  const index = tasks.value.findIndex((t) => t.id === updatedTask.id)
  if (index !== -1) {
    tasks.value[index] = { ...updatedTask }
  }
  showEdit.value = false
}

function handleDeleteTask(task) {
  if (confirm(`Are you sure you want to delete "${task.title}"?`)) {
    tasks.value = tasks.value.filter((t) => t.id !== task.id)
  }
}

// Column updates
function handleAddColumn(newCol) {
  columnRef.value.addColumn(newCol)
  showAddColumn.value = false
}

// -------------------- Lifecycle --------------------
onMounted(() => {
  const saved = localStorage.getItem("Kanban-task")
  if (saved) {
    tasks.value = JSON.parse(saved)
  }
})

watch(
  tasks,
  (newTasks) => {
    localStorage.setItem("Kanban-task", JSON.stringify(newTasks))
  },
  { deep: true }
)
</script>

<template>
  <div class="flex flex-col justify-center items-center">
    <h1 class="mt-[4rem]">Welcome to Kanban Board</h1>

    <Toolbar
      @togglecol="toggleColumnModal"
      @search="handleSearch"
      @assignee="handleAssigneeSearch"
      @theme="toggleTheme"
      :theme="theme"
      :onToggleTheme="toggleTheme"
    />

    <Column
      ref="columnRef"
      :tasks="tasks"
      :showAddColunm="showAddColumn"
      :searchTerm="searchTerm"
      :theme="theme"
      :searchAssine="searchAssignee"
      @openAddTask="openAddTask"
      @updateTasks="updateTasks"
      @editTask="handleEditTask"
      @delete="handleDeleteTask"
    />

    <AddTask
      v-if="showTask"
      :column-id="activeColumnId"
      @addTask="handleAddTask"
      @close="toggleTaskModal"
    />

    <AddColumn
      v-if="showAddColumn"
      @addColumn="handleAddColumn"
      @close="toggleColumnModal"
    />

    <EditTask
      v-if="showEdit"
      v-model="showEdit"
      :task="selectedTask"
      @update-task="handleUpdateTask"
    />
  </div>
</template>
