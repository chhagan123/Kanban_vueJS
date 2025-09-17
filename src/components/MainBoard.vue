<script setup>
import Toolbar from './Toolbar.vue';
import AddTask from '../taskmodel/AddTask.vue';
import { ref } from 'vue';

const ShowTask = ref(false)
function maintoggle () {
    ShowTask.value = !ShowTask.value
}
</script>

<template>
    <div class="flex flex-col justify-center items-center">
        <h1 class="mt-[4rem]">Welcome to Kanban Board</h1>
        <Toolbar @toggle="maintoggle"/>
        <AddTask @close="maintoggle" v-if="ShowTask" />
        
    </div>

</template>