<script setup>
import {computed, onMounted, ref} from "vue";


const history = ref([])   // history pictures
const currentIndex = ref(-1) // The index of the current exhibited picture

const favorites = ref([])// favorite pictures
const isCurrentFavorite = computed(() => {// if the current pic is in favorites, return true
  console.log(currentIndex.value,'aaaaa')
  const currentUrl = history.value[currentIndex.value]
  return favorites.value.includes(currentUrl)
})

const isLoading = ref(false)

const errorMessage = ref('')

async function getCat() {
  isLoading.value = true
  // clear the errorMessage
  errorMessage.value = ''
  try {
    // get the cat picture info from the given api
    const res = await fetch("https://cataas.com/cat?json=true");
    // get the picture url
    const data = await res.json()
    const newUrl = data.url
    if (!res.ok || !newUrl) {
      throw new Error("Failed to fetch Cat Picture" + res.status);
    }
     // limit the length of history to 50
    if (history.value.length >= 50) {
      history.value.shift() // if length gets over 50, the first picture in history will be removed
    }
    history.value.push(newUrl)
    currentIndex.value = history.value.length - 1


  } catch (error) {
    errorMessage.value = '😿 Oops! Failed to load a cat picture. Please try again.'
    console.log(errorMessage.value)
    isLoading.value = false
  }
}

function goBack() {
  errorMessage.value = ''
  if(history.value.length === 1||history.value.length === 0) {// when there's no picture or there's only one picture, do nothing
    return
  }
  isLoading.value=true
  try {

    if(currentIndex.value ===0) {// if it's the first picture in history, go to the last one
      currentIndex.value=history.value.length - 1
    }else{// if it's not the first picture in history, go back
      currentIndex.value--
    }
  }catch(error){
    errorMessage.value = '😿 Oops! Failed to load a cat picture. Please try again.'
    console.log(errorMessage.value)
    isLoading.value = false
  }

}

function goNext() {
  errorMessage.value = ''
  if(history.value.length === 1||history.value.length === 0) {// when there's no picture or there's only one picture, do nothing
    return
  }
  isLoading.value=true
  try {
    if(currentIndex.value ===history.value.length - 1) {// if it's the last picture in history, go to the first one
      currentIndex.value=0
    }else{// if it's not the last picture in history, go next
      currentIndex.value++
    }
  }catch(error){
    errorMessage.value = '😿 Oops! Failed to load a cat picture. Please try again.'
    console.log(errorMessage.value)
    isLoading.value = false
  }
}
function onImageLoad() {
  isLoading.value = false
}

function saveFavorite() {
  const currentUrl = history.value[currentIndex.value]
  const index = favorites.value.indexOf(currentUrl)
  if (index !== -1) {
    // if it has been saved in favorites, removed it from favorites
    favorites.value.splice(index, 1)
  } else {
    // if it has not been saved in favorites, push it into favorites
    if (favorites.value.length >= 50) {// set a number limit
      alert('🐱 You can only save up to 50 favorite cats!')
      return
    }else{
      favorites.value.push(currentUrl)
    }

  }
  // update local storage
  localStorage.setItem('favorites', JSON.stringify(favorites.value))

}

onMounted( () => { // when open or refresh the page, load the favorite pictures from local storage
  const stored = localStorage.getItem('favorites')
    if (stored) {
      favorites.value = JSON.parse(stored)
      history.value= [...favorites.value]
      console.log(history.value)
      currentIndex.value = 0
    }else{ // if there is no pictures in favorite, load a cat picture
      getCat()
    }
  }
)
</script>

<template>
  <div>
    <h1 class="page-title">🐱 Cat Picture App</h1>
  </div>
  <div class="main-layout">
    <div class="image-container">
      <div class="image-wrapper">
        <img :src="history[currentIndex]"
             alt="Cat Picture"
             @load="onImageLoad"
             :class="{'loading':isLoading}"
        />
        <div v-if="isLoading" class="main-overlay" id="loading-overlay">
          <div class="spinner"></div>
          <p>Loading a cat picture...</p>
        </div>
        <div v-if="errorMessage" class="main-overlay" id="error-overlay">
          {{ errorMessage }}
        </div>
      </div>
      <p>
        Image {{ currentIndex + 1 }} of {{ history.length}} (50 🐱 maximum)
      </p>
    </div>

    <div class="operation">
      <button id="get_cat_btn" @click="getCat">🐱 Serve a Cat Picture!</button>
         <button id="favorite_btn" @click="saveFavorite" :disabled="isLoading" >{{ isCurrentFavorite ? '💔 Remove from Favorites' : '❤️ Save to Favorites' }}</button>
      <div class="button-group">
       <button id="back_btn" @click="goBack">
         Back
      </button>
        <button id="next_btn" @click="goNext">
          Next
        </button>
    </div>
  </div>
  </div>
</template>

<style scoped>
.main-layout {
  flex: 1;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-items: flex-start;
  gap: 2rem;
  padding: 1rem;
  box-sizing: border-box;  /* allow rolling*/
}

.image-container {
  flex: 1 1 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 250px;
  max-height: 100%;
}
.operation {
  width: 280px;
  flex-shrink: 0;        /*do not allow compression*/
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.image-wrapper {
  width: 100%;
  max-height:80vh;
  min-width: 280px;
  aspect-ratio: 4 / 3;
  background-color: #f3f3f3;
  border-radius: 10px;
  overflow: hidden;
  position: relative;
}
.image-wrapper img {
  width: 100%;  /*fit the size of wrapper*/
  height: 100%;
  object-fit: contain; /* images displayed without cropping or stretching */
  display: block;
}
.button-group { /*only the back and next button*/
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin: 0 auto;

}

#back_btn, #next_btn {
  width: 135px;
}


</style>
