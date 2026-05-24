<script setup>
import NoteItem from './NoteItem.vue'
defineProps({
    notes:{
        type:Array,
        required:true
    }
})

const emit=defineEmits(['delete','toggle','update'])

// 调用id为子组件传过来的参数,并向其父组件传递
const handleDelete=(id)=>emit('delete',id)
const handleToggle=(id)=>emit('toggle',id)
const handleUpdate=(id,text)=>emit('update',id,text)
</script>
<template>
    <div class="note-list">
        <NoteItem
        v-for="note in notes"
        :key="note.id"
        :note="note"
        @delete="handleDelete"
        @toggle="handleToggle"
        @update="handleUpdate"
        ></NoteItem>
    </div>
</template>
<style scoped>
.note-list{
    height: 200px;
    overflow: scroll;
    margin-bottom: 20px;
}
</style>