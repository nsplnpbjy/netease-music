<template>
  <div>
    <div>
    <audio :src = 'audioSrc'  id="player" preload="auto" controls >
      你的浏览器不支持audio标签
    </audio>
    </div>
    <input v-model="searchText" placeholder="请输入歌曲名"/>
    <button v-on:click="searchMethod">搜索</button>
  </div>
</template>

<script>
import Vue from 'vue';
import axios from 'axios';  //引入axios
import VueAxios from 'vue-axios';  // 引入vue-axios
Vue.use(VueAxios,axios);  //使用
export default {
  name: 'hostPage',
  props: {
    msg: String,
    audioSrc:null,
    searchText:null,
    searchUrl: {
      type: String,
      default: 'http://localhost:8075/search'
    },
    songs:null
  },
  methods:{
        loadSrc(){
              this.audioSrc = "http://music.163.com/song/media/outer/url?id=1373364357.mp3"
        },
        searchMethod(){
          Vue.axios.post(this.searchUrl, {//发送请求 跳转页面
            s: this.searchText,
            offset: '0',
            limit: '20',
            type: '1'
          }).then((response) => {
            this.songs = response.data.data.result.songs
            console.log(this.songs)
          }).catch((response) => {
            console.log("错误"+response.data.msg)
          })
        }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
