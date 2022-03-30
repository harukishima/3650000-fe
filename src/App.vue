<script setup lang="ts">
import {onMounted, ref} from "vue";
import axios from "axios";

interface Response {
  code: number;
  url: string;
}

const mode = {
  1: 'Weibo',
  2: 'Instagram',
  3: 'Cosplay',
  5: 'Mtcos',
  7: 'Legs',
  8: 'Coser',
  9: 'Tuwan',
  66: 'NSFW',
}
const num = ref(15);
const selectedMode = ref(1);

const posts = ref<Response[]>([]);

const getUrl = async (thisMode: number): Promise<Response> => {
  const {data} = await axios.get('https://3650000.xyz/api/', {
    params: {
      mode: thisMode,
      type: 'json',
    }
  });
  return data;
}

const refresh = async () => {
  posts.value = [];
  for (let i = 0; i < num.value; i++) {
    posts.value.push(await getUrl(selectedMode.value))
  }
}
onMounted(() => {
  refresh();
})
</script>

<template>
  <div>
    <form style="position: fixed" @submit.prevent="refresh">
      <label for="num">Num: </label>
      <input type="number" id="num" v-model="num"/>
      <br/>
      <label for="mode">Mode: </label>
      <select id="mode" v-model="selectedMode">
        <option v-for="(value, key) in mode" :key="value" :value="key">{{ value }}</option>
      </select>
      <br/>
      <button type="submit">Refresh</button>
    </form>
    <div class="image-container">
      <img v-for="post in posts" :src="post.url" :key="post.url" alt="photo" loading="lazy"/>
    </div>
  </div>
</template>

<style>
.image-container {
  display: flex;
  justify-content: center;
  flex-direction: column;
}

img {
  max-width: 80%;
  height: auto;
  margin-left: auto;
  margin-right: auto;
}
</style>