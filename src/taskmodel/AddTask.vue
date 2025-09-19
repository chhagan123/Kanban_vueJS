<script setup>
import { ref } from "vue";

const props = defineProps({
  columnId: {
    type: String,
    required: false,   // ✅ make it optional
    default: null,
  },
});


const title = ref("");
const description = ref("");
const Assignees = ref("");
const date = ref("");
const Assignee = ["chhagan", "Nandu", "Rishabh"];

const emit = defineEmits(["close", "addTask"]);

function close() {
  emit("close");
}

function AddTask() {
  const Task = {
    id: Date.now(),
    title: title.value,
    description: description.value,
    Assignees: Assignees.value,
    date: date.value,
    columnId: props.columnId,
  };
  emit("addTask", Task);
  emit("close");
}
</script>

<template>
  <div
    class="fixed inset-0 flex items-center justify-center bg-black/50 backdrop-blur-sm"
  >
    <div class="bg-white w-[400px] p-6 rounded-lg shadow-lg">
      <h2 class="text-xl font-bold mb-4">Add Task</h2>

      <input v-model="title" type="text" placeholder="Enter Title"
        class="border w-full p-2 rounded mb-3" />

      <textarea v-model="description" placeholder="Enter Description"
        class="border w-full p-2 rounded mb-3"></textarea>

      <select v-model="Assignees" class="border w-full p-2 rounded mb-3">
        <option disabled value="">Select Assignee</option>
        <option v-for="(a, index) in Assignee" :key="index" :value="a">
          {{ a }}
        </option>
      </select>

      <input v-model="date" type="date"
        class="border w-full p-2 rounded mb-4" />

      <div class="flex justify-end gap-3">
        <button @click="close"
          class="px-4 py-2 rounded bg-gray-300 hover:bg-gray-400">
          Cancel
        </button>
        <button @click="AddTask"
          class="px-4 py-2 rounded bg-green-500 text-white hover:bg-green-600">
          Add Task
        </button>
      </div>
    </div>
  </div>
</template>
