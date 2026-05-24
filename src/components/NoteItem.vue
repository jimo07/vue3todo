<script setup>
import { ElCheckbox,ElButton,ElInput } from 'element-plus'
import { ref,nextTick } from 'vue'
const props=defineProps({
    note:{
        type:Object,
        required:true
    }
})

const emit=defineEmits(['delete','toggle','update'])

// 编辑状态
const isEditing=ref(false)
const editText=ref(props.note.text)

// 开始编辑
const startEdit=()=>{
    isEditing.value=true
    editText.value=props.note.text
    nextTick(()=>{
        const inputEl=document.querySelector(`.edit-input-${props.note.id}`)
        inputEl?.focus()
    })
}
// 保存编辑
const saveEdit=()=>{
    const trimmed = editText.value.trim()
    if(trimmed && trimmed != props.note.text){
        emit('update',props.note.id,trimmed)
    }
    isEditing.value=false
}
// 取消编辑
const cancelEdit=()=>{
    isEditing.value=false
}
// 增加键盘事件
const onKeydown=(e)=>{
    // 回车保存
    if(e.key==='Enter'){
        saveEdit()
    }
    // esc退出编辑
    if(e.key==='Escape'){
        cancelEdit()
    }
}
// 向父组件NoteList传递id
const handleDelete=()=>emit('delete',props.note.id)
const handleToggle=()=>emit('toggle',props.note.id)


</script>
<template>
    <div class="note-item" :class="{completed: note.completed}">
        <el-checkbox :model-value="note.completed" @change="handleToggle" />
        <span class="note-text"
        v-if="!isEditing"
        @dblclick="startEdit" 
        >{{ note.text }}</span>
        <!-- 编辑模式输入框
        @blur失去焦点-->
        <input
        v-else
        :class="`edit-input-${note.id}`"
        v-model="editText"
        :style="{width:`${editText.length+1}ch`}"
        @blur="saveEdit" 
        @keydown="onKeydown"  
        ></input>
        <el-button type="danger" circle size="small" @click="handleDelete">X</el-button>     
    </div>
</template>
<style scoped>
.note-item{
    position: relative;
    background-color: #fff;
    border: 1px solid rgb(220, 220, 220);
    width: 400px;
    margin: 0px auto;
    border-collapse: collapse;
    height: 30px;
    padding-left: 20px;
    box-sizing: border-box;
}
span,input{
    font-size: 20px;
    line-height: 30px;
    margin-left: 10px;
}
input{
    display: inline-block;
    height: 20px;
    font-size: 18px;
}
input:focus{
    outline: none;
}
.note-item.completed span{
    color: rgb(169, 119, 119);
}
.note-item :deep(.el-button){
    display: none;
    position: absolute;
    background-color: transparent;
    border: none;
    color: rgb(121, 120, 120);
    right: 10px;
    line-height: 30px;
}
.note-item:hover :deep(.el-button){
    display: inline-block;
    cursor: pointer;
}

</style>