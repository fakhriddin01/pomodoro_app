<template>
    <div class="flex justify-center items-center">
        {{ time }}
    </div>
</template>

<script setup>
import dayjs from 'dayjs'

const time = ref('')

setInterval(()=>{
        time.value = dayjs().format('HH:mm:ss') 
},1000)



</script>

<style lang="scss" scoped>

</style>