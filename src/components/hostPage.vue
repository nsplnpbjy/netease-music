<template>
  <div>
    <div>
    <audio :src = 'audioSrc'  id="player" preload="auto" controls :autoplay="playSwitch">
      你的浏览器不支持audio标签
    </audio>
    </div>
    <input v-model="searchText" placeholder="请输入歌曲名"/>
    <button v-on:click="searchMethod">搜索</button>
    <div>
      <el-table :data="songs" style="width: 100%">
        <el-table-column
            prop="name"
            label="歌曲名"
            width="650">
        </el-table-column>
        <el-table-column
            prop="artists[0].name"
            label="艺人"
            width="650">
        </el-table-column>
        <el-table-column label="选择">
          <template slot-scope="scope">
            <el-button
                size="mini"
                @click="loadSrc(scope.row.id,scope.row.name,scope.row.fee)">▶♫</el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import axios from 'axios';  //引入axios
import VueAxios from 'vue-axios';  // 引入vue-axios
import Vue from 'vue'
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'

Vue.use(ElementUI)
Vue.use(VueAxios,axios);  //使用
export default {
  name: 'hostPage',
  props: {
    preSongs:null,
    msg: String,
    audioSrc:null,
    searchText:null,
    playSwitch:{
      type: Boolean,
      default: false,
    },
    searchUrl: {
      type: String,
      default: 'http://192.168.0.111:8075/search'
    },
    songs:{
      type: JSON,
      default: null
    }
  },
  methods:{
         loadSrc:function (id,musicName,fee){
              if (fee==1){
                alert("收费歌曲,无法播放");
                return;
              }
              this.audioSrc = "http://192.168.0.111:8075/music?id="+encodeURIComponent(id+".mp3")+"&musicName="+encodeURIComponent(musicName);
              this.playSwitch=true;

        },
        searchMethod(){
          Vue.axios.post(this.searchUrl, {//发送请求 跳转页面
            s: this.searchText,
            offset: '0',
            limit: '20',
            type: '1'
          }).then((response) => {
            this.songs = response.data.data.result.songs;
            console.log(this.songs);
          }).catch((response) => {
            console.log("错误"+response)
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
