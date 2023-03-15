<script setup>
import {ref, computed} from 'vue' 
import SearchbarResults from '../components/SearchbarResults.vue'
import Pictures from '../components/Pictures.vue';
import axios from 'axios'

const SucessMsg =  ref('Vous avez 5 tentatives')
let numberErr = ref(5)
const search = ref('')
const games = ref('')
const endOfGame = ref(false);
const WinOfGame = ref(false);
const pagenumber = ref(1)
const blurAmount = ref(1)

const randomnumber = computed(() => {
  return  Math.floor(Math.random() * 19) + 1;
})

async function getGames(){
      await axios.get(`https://api.rawg.io/api/games?key=3b9e3e45a7494082aabf45c1bf5f08fa&page=${pagenumber.value}`)
            .then(response => {
                  games.value= response.data.results
            })
            .catch(error => {
                console.log(error)
            })
      }

function getName(name,randomnumber){
      
      if(games.value[randomnumber].name === name ){
            SucessMsg.value = "Réussi"
            WinOfGame.value = !WinOfGame.value;
            blurAmount.value= 0
      }
      else{
            numberErr.value = numberErr.value - 1;
            SucessMsg.value = "FAIL, Il vous reste " + numberErr.value + " tentative(s)";
            blurAmount.value = blurAmount.value - 0.25;
      }
      if(numberErr.value <= 0){
            SucessMsg.value = "Fin de partie, plus de tentatives restantes"
            endOfGame.value = !endOfGame.value
      }

}

function reload(){
      window.location.reload()
}
function nextlvl(){
      pagenumber.value = pagenumber.value + 1;
      getGames();
      WinOfGame.value = false;
      search.value= "";
      numberErr.value = 5;
      SucessMsg.value= 'Vous avez 5 tentatives';
      blurAmount.value = 1
}

function filteredList() {
  return games.value.filter((val) =>
    val.name.toLowerCase().includes(search.value.toLowerCase())
  );
}


getGames();
</script>

<template>
      <div class="container">
            <img class="ImgGuess" :src='games[randomnumber].background_image' :style="{ filter: `blur(${blurAmount}rem)` }" />
            <p>{{ SucessMsg }}</p>
            <div v-if="endOfGame">
                  <p>Réponse : {{ games[randomnumber].name }}</p>
                  <p>Score : {{ pagenumber - 1 }}</p>
                  <button @click="reload()">Restart </button>
            </div>
            <div v-else-if="WinOfGame">
                  <p>YOU WIN</p>
                  <button @click="nextlvl()">Next Level </button>
            </div>    
            <div v-else>
                  <button @click="nextlvl()">Next level </button>
                  <input type="text" v-model="search" autofocus/>

                  <!-- <SearchbarResults :search="search" :games="games" @response="(msg) => SucessMsg = msg"  /> -->

                  <!-- <template v-for="game in games" :key="game.id">
                        <div v-if="game.name.toLowerCase().startsWith(search.toLowerCase())" >
                              <div>
                                    <p @click="getName(game.name, randomnumber)">{{ game.name }}</p>
                              </div>
                        </div>
                        <div v-else-if="!search">
                              <p>No Results !</p>
                        </div>
                  </template> -->

                  <div v-for="game in filteredList()" :key="game.id">
                  <p @click="getName(game.name, randomnumber)">{{ game.name }}</p>
                  </div>
                  <div v-if="search&&!filteredList().length">
                  <p>No results found!</p>
                  </div>
            </div>
      </div>
</template>

<style>
.container{
      display: flex;
      flex-direction: column;
      align-items: center;
}
.ImgGuess{
      width:50%
}
</style>