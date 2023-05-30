<script setup>
import { ref, onMounted } from 'vue';
import { Modal } from 'flowbite-vue'


const focus = ref({
  name: 'Focus',
  src: './assets/icons/ph_brain-fill.svg'
})

const short_break_info = ref({
  name: 'Short Break',
  src: './assets/icons/ph_coffee.svg'
})
const isShowModal = ref(false)
function closeModal() {
  isShowModal.value = false
}
function showModal() {
  isShowModal.value = true
}



const isDark = ref(false)
const isResume = ref(true)
const isSound = ref(true)
const isNotify = ref(true)

const pomodoros = ref(3)
const short_break = ref(5)
const long_break = ref(15)

const minutes = ref(25)
const seconds = ref(0)
const timer = ref({})
const isTimer = ref(false);
const start = () =>{
  if(!isTimer.value){
      if(minutes.value != 0 || seconds.value != 0){
          isTimer.value=true;
          timer.value = setInterval(()=>{
          
            if(minutes.value >= 0){
              seconds.value -= 1;
              if(minutes.value === 0 && seconds.value === 0){
                clearInterval(timer.value);
                isTimer.value=false;
              
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



onMounted(()=>{


})
</script>

<template>



  <!-- ------------------------Modal-------------------------------------------- -->
  <Modal size="md" v-if="isShowModal" @close="closeModal">
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
            <el-input-number v-model="minutes" :min="0" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input" />
          </div>
          <div class="flex items-center justify-between">
            <p>Pomodoros until long break</p>
            <el-input-number v-model="pomodoros" :min="0" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input" />
          </div>
          <div class="flex items-center justify-between">
            <p>Short break length</p>
            <el-input-number v-model="short_break" :min="0" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input" />
          </div>
          <div class="flex items-center justify-between">
            <p>Long break length</p>
            <el-input-number v-model="long_break" :min="0" :max="60" size="small" controls-position="right" @change="handleChange" class="number_input" />
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
  <div class="wrapper w-full min-h-screen bg-[#F2FFF5]" :class="isDark ? 'dark' : ''">
    <div class="container grid place-items-center h-screen mx-auto">

      <div class="timer">

        <div class="status dark2">
          <img v-if="!isDark" src="./assets/icons/ph_coffee.svg" alt="">
          <img v-else src="./assets/icons/ph_coffee_dark.svg" alt="">
          STATUS
        </div>

        <div class="numbers ">
          <h1 :class="isDark ? 'dark' : '' " v-if="minutes<10">0{{ minutes }}</h1>
          <h1 :class="isDark ? 'dark' : '' " v-else> {{ minutes}}</h1>
          <h1 :class="isDark ? 'dark' : '' " v-if="seconds<10">0{{seconds}}</h1>
          <h1 :class="isDark ? 'dark' : ''" v-else>{{seconds}}</h1>
        </div>

        <div class="controls flex gap-3 text-4xl">
          <button @click="showModal" class="p-6 bg-[#4DDA6E26] rounded-[24px]"><i class='bx bx-dots-horizontal-rounded'></i></button>
          <button @click="start" class="p-6 bg-[#4DDA6E26] rounded-[24px]"><i v-show="!isTimer" class='bx bx-play'></i><i v-show="isTimer" class='bx bx-pause'></i></button>
          <button class="p-6 bg-[#4DDA6E26] rounded-[24px]"><i class='bx bx-fast-forward'></i></button>
        </div>

      </div>
    </div>
    
  </div>
</template>

<style scoped>

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

  }

  .controls button:focus {
    background-color: #4DDA6E9E;
    font-size: 48px;
  }
  .controls button:hover {
    background-color: #4DDA6E9E;
  }

  .numbers{
    margin: 32px 0;
  }
  .numbers h1{
    font-size:256px;
    line-height: 217px;
    font-weight: 800;
    color: #14401D;
    
  }

  .status{
    width: 197px;
    height: 48px;
    border: 2px solid #14401D;
    border-radius: 48px;
    display: flex;
    justify-content:center;
    align-items: center;
    gap: 20px;
    background-color: #4DDA6E26;
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

  /* .darkicon{
    color: #F2FFF5;
  } */
</style>
