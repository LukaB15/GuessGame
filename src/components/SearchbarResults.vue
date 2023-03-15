<script setup>
defineProps({
      search: {
            type: String,
            required:true
      },
      games:{
            type:Object,
            required:true
      }
})



function getName(name){
return name
}




</script>

<template>
      <template v-for="game in games" :key="game.id">
            <div v-if="game.name.toLowerCase().startsWith(search.toLowerCase())" >
                  <div>
                        <p @click="getName(game.name)">{{ game.name }}</p>
                  </div>
            </div>
            <div v-else-if="!search">
                  <p>No Results !</p>
            </div>
      </template>
</template>