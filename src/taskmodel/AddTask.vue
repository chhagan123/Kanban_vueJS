<script setup>
import { ref } from 'vue'

const title = ref("")
const description = ref("")
const Assignees = ref(["Chhagan", "Nandu", "Rishabh"])
const date = ref("")
 
const emit = defineEmits("close")
function close() {
    emit("close")
}

</script>

<template>
  <div class="fixed inset-0 flex items-center justify-center bg-black/30 backdrop-blur-sm">
    <div class="bg-[#0F172A] text-white w-[28rem] p-6 rounded-2xl shadow-lg">
      
      <!-- Header -->
      <h2 class="text-xl font-bold text-pink-500 flex items-center gap-2 mb-4">
        ✨ Add New Task
      </h2>

      <!-- Title -->
      <div class="mb-4">
        <label class="block text-sm mb-1">Title</label>
        <input 
          v-model="title"
          type="text" 
          placeholder="Enter task title"
          class="w-full px-3 py-2 rounded-md bg-[#1E293B] border border-gray-700 focus:outline-none focus:ring-2 focus:ring-pink-500"
        />
      </div>

      <!-- Description -->
      <div class="mb-4">
        <label class="block text-sm mb-1">Description</label>
        <textarea
          v-model="description"
          placeholder="Enter task description"
          rows="3"
          class="w-full px-3 py-2 rounded-md bg-[#1E293B] border border-gray-700 focus:outline-none focus:ring-2 focus:ring-pink-500"
        ></textarea>
      </div>

      <!-- Assignee -->
      <div class="mb-4">
        <label class="block text-sm mb-1">Assign To</label>
        <select 
          v-model="assignee"
          class="w-full px-3 py-2 rounded-md bg-[#1E293B] border border-gray-700 focus:outline-none focus:ring-2 focus:ring-pink-500"
        >
          <option disabled value="">Select Assignee</option>
          <option v-for="person in Assignees" :key="person">{{ person }}</option>
        </select>
      </div>

      <!-- Due Date -->
      <div class="mb-6">
        <label class="block text-sm mb-1">Due Date</label>
        <input 
          v-model="date"
          type="datetime-local" 
          class="w-full px-3 py-2 rounded-md bg-[#1E293B] border border-gray-700 focus:outline-none focus:ring-2 focus:ring-pink-500"
        />
      </div>

      <!-- Buttons -->
      <div class="flex justify-end gap-3">
        <button 
          @click="close"
          class="bg-gray-200 text-black px-4 py-2 rounded-md hover:bg-gray-300"
        >
          Cancel
        </button>
        <button 
          class="bg-indigo-600 px-4 py-2 rounded-md hover:bg-indigo-700"
        >
          Add Task
        </button>
      </div>

    </div>
  </div>
</template>
