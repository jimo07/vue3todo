<script setup>
import { ElButton,ElRadioButton,ElRadioGroup } from 'element-plus';
defineProps({
    remaining:{
        type:Number,
        default:0
    },
    filterType:{
        type:String,
        required:true
    }
})

const emit=defineEmits(['clean','filter'])

const handleClean=()=>emit('clean')
const handleFilter=(type)=>emit('filter',type)

</script>
<template>
<div class="note-stats">
    <el-radio-group
    :model-value="filterType"
    @change="handleFilter"
    class="note-filter"
    >
        <el-radio-button label="all">全部</el-radio-button>
        <el-radio-button label="active">未完成</el-radio-button>
        <el-radio-button label="completed">已完成</el-radio-button>
    </el-radio-group>

    <div class="note-under">
        <p class="count" v-if="remaining>0">{{ remaining }} {{ remaining===1?'Item Remained':'Items Remained' }}</p>
        <p v-else class="count">All Finshed</p>
        <el-button @click="handleClean">Clean All</el-button>
    </div>
</div>
</template>
<style scoped>
.note-filter{
    display: flex;
    justify-content: center;
    margin-top: 20px;
}
.note-under{
    display: flex;
    justify-content: center;
    align-items: center;
    letter-spacing: 1px;
    gap: 10px;
    font-size: 13px;
    color: rgb(148, 149, 150);
}
.note-stats :deep(.el-button){
    background-color: transparent;
    letter-spacing: 1px;
    border: none;
    color: rgb(148, 149, 150);
    cursor: pointer;
    font-weight: 100;
    font-size: 15px;
}

</style>