<script setup>
import { ref } from 'vue'
defineProps({
    inputMode:{
        type:String,
        default:'add'
    }
})
// 接收方法
const emit=defineEmits(['add','search','mode'])

const addText=ref('')
const searchText=ref('')

const handleAdd=()=>{
    if(addText.value.trim()){
        // 向父组件传输inputText.value
        emit('add',addText.value)
        addText.value=''
    }
}
const handleSearch=()=>{
    emit('search',searchText.value)
}
const handleMode=(type)=>{
    emit('mode',type)
    // 清空另一个输入框
    if(mode==='add'){
        searchText.value=''
    }else{
        addText.value=''
    }
}

</script>
<template>
    <div class="note-header">
        <div class="input-wrapper">
            <!-- 必须要有v-model 双向绑定输入内容-->
            <input
            type="text"
            v-if="inputMode==='add'"
            v-model="addText"
            placeholder="请诉苦水"
            @keyup.enter="handleAdd"
            class="note-input"
            >
            <input
            type="text"
            v-else
            v-model="searchText"
            placeholder="聊便查寻"
            @input="handleSearch"
            class="note-input"
            >
        </div>
        <div class="mode-switch">
            <button
            @click="handleMode('add')"
            :class="{ active:inputMode==='add' }"
            class="icon-btn1"
            >
                ✏️
            </button>
            <button
            @click="handleMode('search')"
            :class="{ active:inputMode==='search' }"
            class="icon-btn2"
            >
                🔍
            </button>
        </div>
    </div>
</template>
<style scoped>
.note-header{
    position: relative;
    display: flex;
    justify-content: center;
}
.input-wrapper{
    width: 100%;
    max-width: 400px;
    height: 35px;
}
.input-wrapper input{
    width: 100%;
    height: 35px;
    font-size: 16px;
    padding-left: 10px;
    box-sizing: border-box;
    border: 1px solid rgb(220, 220, 220);
    border-radius: 10px 10px 0 0;
}
input:focus{
    outline: none;
}
.mode-switch{
    position: absolute;
    right: 5%;
   
}
button{
    margin-left:8px;
    height: 35px;
    width: 35px;
    font-size: 20px;
    background-color: transparent;
    border: none;
    box-shadow: 0 3px 10px rgba(0,0,0,0.1);
    border-radius: 18px;
    cursor: pointer;
}


</style>