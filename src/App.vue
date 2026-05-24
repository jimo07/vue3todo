<script setup>
import { ref,watch,computed,onMounted } from 'vue'
import NoteHeader from './components/NoteHeader.vue'
import NoteList from './components/NoteList.vue'
import NoteStats from './components/NoteStats.vue'

// 记事本数据
const notes=ref([])

// 本地存储
const storeKey='notebook'

// 加载本地数据
const loadNote = ()=>{
  const savedNote = localStorage.getItem(storeKey)
  if(savedNote){
    notes.value=JSON.parse(savedNote)
  }else{
    // 给初始值
    notes.value=[
      {id:1,text:"吃饭饭",completed:false},
      {id:2,text:"睡觉觉",completed:false}
    ]
  }
}

// 保存到本地存储
const savenote = ()=>{
  localStorage.setItem(storeKey,JSON.stringify(notes.value))
}

// 计算完成百分比
const completionRate=computed(()=>{
  if(notes.value.length===0) return 0
  const completedCount=notes.value.filter(note=>note.completed).length
  return Math.round(completedCount/notes.value.length*100)
})
// 圆环参数
const radius=180   //圆环半径px
const circumference = 2*Math.PI*radius    //圆周长
// 计算偏移量
const dashoffset = computed(()=>{
  return circumference*(1-completionRate.value/100)
})

// 增加记事
const addNote=(text)=>{
  if(!text.trim()) return
  const newNote={
    id:Date.now(),
    text:text.trim(),
    completed:false
  }
  notes.value.push(newNote)
}

// 搜索记事
// computed不接受参数
const searchKeyword=ref('')
const searchWord=(keyword)=>{
  searchKeyword.value=keyword
}
const searchNote=computed(()=>{
  if(!searchKeyword.value.trim()) return notes.value
  const kw=searchKeyword.value.trim().toLowerCase()
  const searched=notes.value.filter(note=>note.text.toLowerCase().includes(kw))
  return searched
})
const inputMode=ref('add')
// 切换搜索模式
const setMode=(mode)=>{
    inputMode.value=mode
}

// 删除单个记事
const deleteNote=(id)=>{
  notes.value=notes.value.filter(note=>note.id!==id)
}
// 编辑单个记事
const updateNote=(id,text)=>{
  const note=notes.value.find(note=>note.id===id)
  if(note){
    note.text=text
  }
}

// 切换完成状态
const toggleNote=(id)=>{
  const note=notes.value.find(note=>note.id===id)
  if(note){
    note.completed=!note.completed
  }
}

// 清空所有
const clearAll=()=>{
  notes.value=[]
}

// 筛选（呈现所有、未完成、已完成）computed - 响应，不能改变notes.value
// const filterAll=computer(()=>{
//   return notes.value
// })
// const filterFalse=computed(()=>{
//   return notes.value.filter((note)=>!note.completed)
// })
// const filterTrue=computed(()=>{
//   return notes.value.filter((note)=>note.completed)
// })
// 根据状态计算过滤
// 定义初始状态
const filterType=ref('all')
const filternotes=computed(()=>{
  if(filterType.value==='active'){
    return searchNote.value.filter((note)=>!note.completed)
  }
  if(filterType.value==='completed'){
    return searchNote.value.filter((note)=>note.completed)
  }
  return searchNote.value
})
const setFilter=(type)=>{filterType.value=type}


// 统计未完成信息
const stats=computed(()=>{
  const remainingCount = notes.value.filter(note=>!note.completed).length
  return { remainingCount }
})

// 监听数据
watch(notes,savenote,{deep:true})

// 初始化加载
onMounted(loadNote)
</script>

<template>
  <!-- 外层圆形边框 -->
  <div class="app-ring">
    <svg class="progress-ring" width="500" height="500" view-box="0 0 500 500">
      <!-- 背景灰色圆环 -->
       <!-- cx="140"、cy="140" 表示圆心在 (140,140) -->
      <circle
      cx="250" cy="250"
      :r="radius"
      fill="none"
      stroke="#e4e7ed"
      stroke-width="16"
      />
      <!-- 彩色进度圆环 -->
      <!-- stroke-linecap="round"让圆环断点为圆形 -->
      <!-- stroke-dasharray="circumference"将圆环的边框绘制为虚线，长度为周长，被实线覆盖 -->
      <!-- transform="rotate(-90 140 140)"起点逆时针旋转90度，参数140 140 为旋转中心 -->
      <circle
      cx="250" cy="250"
      :r="radius"
      fill="none"
      stroke="#60b08c"
      stroke-width="16"
      stroke-linecap="round"
      :stroke-dasharray="circumference"
      :stroke-dashoffset="dashoffset"
      transform="rotate(-90 250 250)"
      class="progress-cicle"
      />
    </svg>
    <div id="todo">
      <h1>悲惨大学生苦水记事薄</h1>
      <NoteHeader
      :inputMode="inputMode"
      @add="addNote"
      @search="searchWord"
      @mode="setMode"
      ></NoteHeader>
      <NoteList
      :notes="filternotes"
      @delete="deleteNote"
      @toggle="toggleNote"
      @update="updateNote"
      ></NoteList>
      <NoteStats
      :filterType="filterType"
      @filter="setFilter"
      :remaining="stats.remainingCount"
      @clean="clearAll"
      ></NoteStats>
    </div>
  </div>
 
</template>

<style scoped>
.app-ring {
  position: relative;
  width: 100%;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 40px 20px;
  box-sizing: border-box;
}
.progress-ring{
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);
  width: min(90vw,90vh,500px);
  height: auto;
  z-index: 2;
  /* 让圆环不干扰点击内部按钮 */
  pointer-events: none;
  opacity: 0.2;
}
#todo{
  position: relative;
  z-index: 1;
  font-family: 'Times New Roman', Times, serif;
  width: 100%;
  max-width: 700px;
  max-height: 500px;
  border-radius: 32px;
  padding: 24px 20px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.2);
  box-sizing: border-box;
  border: 1px solid rgb(245, 194, 122);
  background-color: rgb(255, 247, 233);
}
h1 {
  text-align: center;
  color: brown;
}

</style>
