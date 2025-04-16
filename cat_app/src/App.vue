<script setup>
import {ref} from "vue";

const history = ref([])
const currentIndex = ref(-1)
const errorMessage = ref('')

async function getCat() {
  try {
    // get the cat picture info from the given api
    const res = await fetch("https://cataas.com/cat?json=true");
    // get the picture url
    const data = await res.json()
    const newUrl = data.url
    if (!res.ok || !newUrl) {
      throw new Error("Failed to fetch Cat Picture" + res.status);
    }
    history.value.push(newUrl)
    currentIndex.value = history.value.length - 1


  } catch (error) {
    errorMessage.value = '😿 Oops! Failed to load a cat picture. Please try again.'
    console.log(errorMessage.value)
  }
}

function goBack() {

}

// 下一张
function goNext() {

}

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
        />
      </div>
      <p>Image {{ currentIndex + 1 }} of {{ history.length}}(Only the latest 50 pictures are saved in history)</p>
    </div>

    <div class="operation">
      <button id="get_cat_btn" @click="getCat">🐱 Serve a Cat Picture!</button>
     <div class="button-group">
       <button id="back_btn" @click="goBack" :disabled="currentIndex <= 0">
         Back
      </button>
        <button id="next_btn" @click="goNext" :disabled="currentIndex >= history.length - 1">
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
button {
  font-size: 18px;
  cursor: pointer;
  border-radius: 10px;
  border: none;
  height:45px;
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
