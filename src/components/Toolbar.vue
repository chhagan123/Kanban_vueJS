
<script setup>
import { ref } from 'vue';

const Assignees = ref(["chhagan","Nandu","Rishabh"])

const emit= defineEmits(["toggle","togglecol"])

function TogglAddTaskemit () {
    emit("toggle")
}
function togglecol() {
    emit("togglecol")
}

</script>

<template>

    <div class="flex border justify-between border-red-500  h-[3rem] w-[80rem]  ">
        <div class="flex gap-2 justify-start items-center">
            <input class=" ml-[2rem] h-[2rem] border " placeholder="Search by Title" />
            <select class="border  w-[5rem] h-[2rem]">
                <option v-for="person in Assignees" 
                        :key="person"
                        :value="person"
                >{{ person }}</option>
            </select>

        </div>

        <div class="flex justify-end gap-5 items-center ">
           
            <button class="border w-[auto] h-[2rem]" @click="togglecol" >Add Colunm</button>
            <button  class="border">undo</button>
            <button class="border">redo</button>
            <button class="border">Mode</button>

        </div>

    </div>
</template>