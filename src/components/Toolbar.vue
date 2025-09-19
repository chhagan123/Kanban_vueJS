<script setup>
import { ref } from 'vue'

const searchquery = ref("")
const SearchQueryAssgnee = ref("")


const Assignees = ref(["chhagan", "Nandu", "Rishabh"])

const emit = defineEmits(["toggle", "togglecol","search","assignee"])

// emit serchquery 
function handlesearch () {
  console.log(searchquery.value)
  emit("search",searchquery.value)
}
function searchAssignee () {
  console.log(SearchQueryAssgnee.value)
  emit("assignee",SearchQueryAssgnee.value)
}

function TogglAddTaskemit() {
  emit("toggle")
}
function togglecol() {
  emit("togglecol")
}
</script>

<template>
  <div
    class="flex flex-col sm:flex-row justify-between items-center bg-gray-50  shadow rounded-lg px-4 py-3 w-full gap-3 sm:gap-6"
  >
    <!-- Left Side: Search + Dropdown -->
    <div class="flex flex-col sm:flex-row gap-3 w-full sm:w-auto items-center">
      <!-- Search -->
      <div class="relative w-full sm:w-56">
        <input
          v-model="searchquery"
          @input="handlesearch"
          class="pl-3 pr-8 py-2 border rounded-lg text-sm w-full focus:outline-none focus:ring-2 focus:ring-blue-500"
          placeholder="Search by title..."
        />
        <span class="absolute right-2 top-2 text-gray-500 text-sm">🔍</span>
      </div>

      <!-- Dropdown -->
      <select
        v-model="SearchQueryAssgnee"
      
        @change=" searchAssignee"
        class="border rounded-lg px-2 py-2 text-sm w-full sm:w-auto focus:outline-none focus:ring-2 focus:ring-blue-500"
      >
        
        <option disabled value="">Search Assignee</option>
        <option  v-for="person in Assignees" :key="person" :value="person">
          {{ person }}
        </option>
      </select>
    </div>

    <!-- Right Side: Buttons -->
    <div
      class="flex flex-wrap justify-center sm:justify-end gap-2 sm:gap-3 items-center w-full sm:w-auto"
    >
      <!-- Add Column -->
      <button
        @click="togglecol"
        class="bg-blue-600 text-white px-4 py-2 rounded-lg text-sm font-medium hover:bg-blue-700 w-full sm:w-auto"
      >
        ➕ Add Column
      </button>

      <!-- Undo -->
      <button
        class="border px-4 py-2 rounded-lg text-sm font-medium hover:bg-gray-100 flex items-center gap-1 w-full sm:w-auto justify-center"
      >
        ⟲ Undo
      </button>

      <!-- Redo -->
      <button
        class="border px-4 py-2 rounded-lg text-sm font-medium hover:bg-gray-100 flex items-center gap-1 w-full sm:w-auto justify-center"
      >
        ⟳ Redo
      </button>

      <!-- Mode -->
      <button
        class="bg-yellow-400 text-black px-4 py-2 rounded-lg text-sm font-medium hover:bg-yellow-500 w-full sm:w-auto"
      >
        ☀️
      </button>
    </div>
  </div>
</template>
