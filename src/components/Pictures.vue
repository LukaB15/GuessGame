<script setup>
import {computed} from 'vue'
defineProps({
      games:{
            type:Object,
            required:true
      }
})

const randomnumber = computed(() => {
  return  Math.floor(Math.random() * 20);
})
</script>

<template>
      <img class="ImgGuess" :src='games[randomnumber].background_image'/>
</template>

<style qcoped>
.ImgGuess{
      width:50%
}
</style>