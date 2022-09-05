<template>
  <div class="wrapper">
    <div class="container">
      <div class="header"><span>Only for cat lovers!</span></div>
      <div class="content">
        <input v-model="searchPhrase" @input="fetchData" class="search-field" type="text"
               placeholder="Введите фразу на английском...">
        <div v-if="order">
          <video ref="video" :src="curVideoSrc" @ended="next" autoplay></video>
          <div class="row-flex-center">
            <a href="#" class="invert control-btn" v-if="prevVideoData" @click="prev"><img width="20" src="@/assets/img/next.svg" alt=""></a>
            <a href="#" class="invert control-btn" disabled v-if="!prevVideoData" @click="prev"><img width="20" src="@/assets/img/next.svg" alt=""></a>
            <p class="nums"><span>{{ order }}</span>/<span>{{ videoLength }}</span></p>
            <a href="#" class="control-btn" v-if="nextVideoData" @click="next"><img width="20" src="@/assets/img/next.svg" alt=""></a>
            <a href="#"  class="control-btn" disabled v-if="!nextVideoData" @click="next"><img width="20" src="@/assets/img/next.svg" alt=""></a>
          </div>
            <p class="sub"><span>{{ curSubtitles }}</span></p>
        </div>
        <div v-if="!order" class="cat-logo"><img src="/src/assets/logo.png" alt=""></div>
      </div>
      <div class="footer"><span>Oleg Kyzlasov 2022 ©</span></div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const searchPhrase = ref(null);

const curVideoSrc = ref(null);

const curSubtitles = ref(null);

const videoLength = ref(null);

const nextVideoData = ref(null);

const prevVideoData = ref(null);

const filmNums = ref(0);

const video = ref(null);

const order = ref(0);

const next = () => {
  if (nextVideoData.value !== null) {
    axios.get(nextVideoData.value)
        .then(response => {
          order.value++;
          curVideoSrc.value = "http://localhost/video_clips/" + response.data.data[0].name;
          curSubtitles.value = response.data.data[0].text;
          nextVideoData.value = response.data.next_page_url;
          prevVideoData.value = response.data.prev_page_url;
        }
    )
  }
}

const prev = () => {
  if (prevVideoData.value !== null) {
    axios.get(prevVideoData.value)
        .then(response => {
              order.value--;
              curVideoSrc.value = "http://localhost/video_clips/" + response.data.data[0].name;
              curSubtitles.value = response.data.data[0].text;
              nextVideoData.value = response.data.next_page_url;
              prevVideoData.value = response.data.prev_page_url;
            }
        )
  }
}

const fetchData = () => {
  order.value = 0;
  curVideoSrc.value = "";
  nextVideoData.value = null;
  prevVideoData.value = null;
  videoLength.value = null;

  axios.get(`http://localhost/api/video/${searchPhrase.value}`)
      .then(response => {
        if (response.data.data.length > 0) {
          order.value++;
          curVideoSrc.value = "http://localhost/video_clips/" + response.data.data[0].name;
          curSubtitles.value = response.data.data[0].text;
          nextVideoData.value = response.data.next_page_url;
          prevVideoData.value = response.data.prev_page_url;
          axios.get(`http://localhost/api/video/${searchPhrase.value}/count`)
              .then(response => videoLength.value = Number(response.data));
        }
      });
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Lobster&display=swap');
@import url('https://fonts.googleapis.com/css2?family=Alfa+Slab+One&display=swap');

.header {
  font-family: 'Lobster', cursive;
  width: 100%;
  background: #71829e;
  height: 30px;
  display: flex;
  justify-content: center;
  border-radius: 15px 15px 0 0;
}

.header > span {
  font-size: 20px;
  color: white;
}

.footer {
  font-family: 'Lobster', cursive;
  width: 100%;
  background: #71829e;
  height: 30px;
  border-radius: 0 0 15px 15px;
  text-align: center;
}

.footer > span {
  font-size: 16px;
  color: white;
}

.content {
  height: 100%;
  padding: 20px;
  border-bottom: none;
  border-top: none;
}

.wrapper {
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 391px;
  width: 50%;
  max-width: 400px;
  border-radius: 15px;
  background: #eef3f7;
  box-shadow: 0px 15px 35px -5px rgba(50, 88, 130, 0.32);
}

.search-field {
  width: 100%;
  height: 30px;
  font-size: 16px;
  outline: none;
  border-radius: 15px;
  text-indent: 10px;
  border: 2px solid lightgray;
  color: #71829e;
  text-align: center;
}

.search-field:focus {
  border-color: gray;
}

.search-field:focus::-webkit-input-placeholder {
  color: transparent
}

.search-field:focus::-moz-placeholder {
  color: transparent
}

.search-field:focus:-moz-placeholder {
  color: transparent
}

.search-field:focus:-ms-input-placeholder {
  color: transparent
}

video {
  border: none;
  width: 100%;
  border-radius: 15px;
  padding-top: 5px;
}

.muted {
  filter: grayscale(1);
}

.nums {
  font-weight: bold;
  padding: 0 10px 0 10px;
}

.sub {
  font-family: 'Alfa Slab One', cursive;
  padding-top: 10px;
  width: 100%;
  text-align: center;
}

.invert {
  transform: scale(-1, 1);
}

.cat-logo {
  display: flex;
  justify-content: center;
  padding: 15px;
  margin-top: 50px;
}

.row-flex-center {
  display: flex;
  justify-content: center;
}

.control-btn {
  padding: 3px;
}
@media (max-width: 550px) {
  .container {
    width: 90%;
  }
}
</style>
