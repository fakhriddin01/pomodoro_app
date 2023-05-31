<script setup>
import { ref, onMounted, reactive, computed } from 'vue';
import { Modal } from 'flowbite-vue'
import work from './assets/notifications/work.wav';
import breake from './assets/notifications/breake.wav';
import storm from './assets/notifications/storm.wav';

const isShowModal = ref(false)
function closeModal() {
  isShowModal.value = false
}
function showModal() {
  isShowModal.value = true
}



const isDark = ref(false)
const isResume = ref(false)
const isSound = ref(true)
const isNotify = ref(true)

const pomodoros = ref(2)
const pomodorosRun = ref(0)
const short_break = ref(4)
const focus = ref(2)
const minutes = ref(focus.value)
const seconds = ref(0)
const timer = ref({})
const isTimer = ref(false);

const statusText = ref('Focus')

const setColor = reactive({
  bg:'#FFF2F2',
  hover: '#FF4C4CB5',
  button: '#FF4C4C26',
  number: '#471515'
})


const start = () =>{
  if(!isTimer.value){
    statusText.value == 'Focus' ? minutes.value = focus.value : minutes.value = short_break.value
      if(minutes.value != 0 || seconds.value != 0){
          isTimer.value=true;
          timer.value = setInterval(()=>{
          
            if(minutes.value >= 0){
              seconds.value -= 1;
              if(minutes.value === 0 && seconds.value === 0){
                clearInterval(timer.value);
                isTimer.value=false;
                if(statusText.value == 'Focus'){
                  pomodorosRun.value ++
                }
                if(pomodorosRun.value != pomodoros.value){
                  checkStatus()
                }
                else{
                  const sound = document.querySelector('#workNotify')
                  isNotify.value ? sound.play() : ''
                  pomodorosRun.value = 0
                  pomodorosNotify()
                }

              }
              
              if(seconds.value == -1){
                seconds.value = 59;
                minutes.value -= 1;
              }
            }
          
        
        }, 100)
      }   
  }else{
    clearInterval(timer.value)
    isTimer.value=false
  }
  
}

const refresh = () => {
  if(statusText.value == 'Focus'){
    minutes.value = focus.value;      
  }else{
    minutes.value = short_break.value;
  }
}


const checkStatus = () => {
  if(statusText.value == 'Focus'){
      const sound = document.querySelector('#workNotify')
      isNotify.value ? sound.play() : breakNotification()
      minutes.value = short_break.value;
      statusText.value = 'Break'
      setColor.bg = '#F2FFF5'
      setColor.hover = '#4DDA6E9E'
      setColor.button = '#4DDA6E26'
      setColor.number = '#14401D'
      if(isResume.value){
        setTimeout(()=>{
          start()
        }, 2500)
      }
      return
    }else{
      const sound = document.querySelector('#breakeNotify')
      isNotify.value ? sound.play() : workNotification()
      minutes.value = focus.value;
      statusText.value = 'Focus'
      setColor.bg = '#FFF2F2'
      setColor.hover = '#FF4C4CB5'
      setColor.button = '#FF4C4C26'
      setColor.number = '#471515'
      if(isResume.value){
        setTimeout(()=>{
          start()
        }, 2500)
      }
      return
    }
}

const refreshButton = () =>{
  if(isTimer.value){
    start()
    seconds.value=0
    refresh()
  }
  else{
    seconds.value = 0
    refresh()
  }
}

const next = () =>{
  if(isTimer.value){
    start()
    seconds.value=0
    checkStatus()
  }
  else{
    seconds.value = 0
    checkStatus()
  }
}


const pomodorosNotify = () => {
  ElNotification({
    title: 'Excellent',
    message: `You have worked ${pomodoros.value} pomodoros`,
    type: 'success',
  })
}

const workNotification = () => {
  ElNotification({
    title: 'Work Time',
    message: `It is time to work buddy`,
    type: 'info',
  })
}

const breakNotification = () => {
  ElNotification({
    title: 'Break Time',
    message: `Take a break you deserved it !`,
    type: 'success',
  })
}
</script>

<template>


<div class="reletive">
    
  
  <!-- ------------------------Modal-------------------------------------------- -->
   <Modal size="md" v-if="isShowModal" @close="closeModal" >
      <template #header>
        <div class="flex items-center text-lg font-bold">
          Settings
        </div>
      </template>
      <template #body>
        
        <div class="flex flex-col items-between justify-between gap-2 ">
          <div class="flex items-center justify-between">
            <p>Dark Mode</p>
            <el-switch v-model="isDark" />
          </div>

          <div class="flex items-center justify-between">
            <p>Focus length</p>
            <el-input-number v-model="focus" :min="0" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input " />
          </div>
          <div class="flex items-center justify-between">
            <p>Pomodoros</p>
            <el-input-number v-model="pomodoros" :min="1" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input" />
          </div>
          <div class="flex items-center justify-between">
            <p>Short break length</p>
            <el-input-number v-model="short_break" :min="0" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input" />
          </div>

          <div class="flex items-center justify-between">
            <p>Auto resume timer</p>
            <el-switch v-model="isResume" />
          </div>
          <div class="flex items-center justify-between">
            <p>Sound</p>
            <el-switch v-model="isSound" />
          </div>
          <div class="flex items-center justify-between">
            <p>Notifications</p>
            <el-switch v-model="isNotify" />
          </div>
        </div>
        
      </template>
      
    </Modal>
 
  <!-- ------------------------Modal END-------------------------------------------- -->
  <div class="wrapper w-full min-h-screen" :class="isDark ? 'dark' : ''" >
  <audio :src="work" id="workNotify" class="w-0 h-0 overflow-hidden" controls></audio>
  <audio :src="breake" id="breakeNotify" class="w-0 h-0 overflow-hidden" controls></audio>
  <audio :src="storm" id="storm" class="w-0 h-0 overflow-hidden"  controls></audio>
    <div class="container relative grid place-items-center h-screen mx-auto">
  
      <i @click="showModal" class='bx bx-cog absolute right-2 top-2 text-2xl'></i>
      <div class="timer">
        <div class="status">
          
          <i v-if="statusText == 'Break'" class='bx bx-coffee text-3xl' :class="isDark ? 'text-[#F2FFF5]' : ''"></i>
          <i v-else class='bx bx-brain text-3xl' :class="isDark ? 'text-[#F2FFF5]' : ''"></i>
          <p class="text-xl"> {{ statusText }}</p>
        </div>

        <div class="numbers ">
          <h1 :class="isDark ? 'dark' : '' " v-if="minutes<10">0{{ minutes }}</h1>
          <h1 :class="isDark ? 'dark' : '' " v-else> {{ minutes}}</h1>
          <h1 :class="isDark ? 'dark' : '' " v-if="seconds<10">0{{seconds}}</h1>
          <h1 :class="isDark ? 'dark' : ''" v-else>{{seconds}}</h1>
        </div>

        <div class="controls flex gap-3 text-4xl">
          <button @click="refreshButton" class="p-6 rounded-[24px]"><i class='bx bx-refresh'></i></button>
          <button @click="start" class="p-6  rounded-[24px]"><i v-show="!isTimer" class='bx bx-play'></i><i v-show="isTimer" class='bx bx-pause'></i></button>
          <button @click="next" class="p-6 rounded-[24px]"><i class='bx bx-fast-forward'></i></button>
        </div>

      </div>
    </div>
    
  </div>
</div>

 
</template>

<style scoped>

  .wrapper{
    background-color: v-bind('setColor.bg');
  }
  .timer{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  .controls {
    height: 100px;
    align-items: center;
  }
  .controls button {
    transition: all 0.3s ease;
    background-color: v-bind('setColor.button');

  }

  .controls button:focus {
    background-color: v-bind('setColor.button');
    font-size: 48px;
  }
  .controls button:hover {
    background-color: v-bind('setColor.hover');
  }

  .numbers{
    margin: 32px 0;
  }
  .numbers h1{
    font-size:256px;
    line-height: 217px;
    font-weight: 800;
    color: v-bind('setColor.number');
    
  }

  .status{
    /* width: 197px; */
    padding: 5px 18px;
    height: 48px;
    border: 2px solid;
    border-color: v-bind('setColor.number');
    border-radius: 48px;
    display: flex;
    justify-content:center;
    align-items: center;
    gap: 20px;
    background-color: v-bind('setColor.button');
  }

  .number_input{
    scale: 1.2;
    width: 70px;
    
  }

  .dark{
    background-color: #040D06 !important;
    color: #F2FFF5 !important;
  }

  .dark2{
    border: 2px solid #F2FFF5;
    /* border:  !important; */
  }

</style>
