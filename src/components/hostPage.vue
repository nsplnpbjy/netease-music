<template>
  <div>
    <div>
      <img alt="logo" src="../assets/logo.png" class="pic" :hidden="logoHide">
    </div>
    <div>
      <img ref="pic" class="pic" alt="pic" :src="picUrl" :hidden="picHide">
    </div>
    <div :hidden="loadingHide">
      <div>
      <img class="pic" alt="loading" src='../assets/loading.gif' >
      </div>
      <div>
        <p>...正在加载，请稍后...</p>
      </div>
    </div>
    <div>
    <audio ref="audioRef" :src = 'audioSrc'  id="player" preload="auto" controls @timeupdate="timeUpDate"  @loadstart="loadNew()" @canplay="canplay()" @ended="playNext()" >
      你的浏览器不支持audio标签
    </audio>
    </div>
    <input v-model="searchText" placeholder="请输入歌曲名"/>
    <button v-on:click="searchMethod">搜索</button>
    <div>
      <textarea class="audio" v-model="showingLyric" readonly="readonly" style="margin: 0px; height: 100px; width: 100%; resize: none; border: none" :hidden="isNotShowingLyric">

      </textarea>
    </div>
    <div>
      <div>
        <el-divider></el-divider>
      </div>
      <el-button type="primary" @click="showDrawer = true">播放列表</el-button>
      <el-drawer
          title="播放列表"
          :visible.sync="showDrawer"
          direction="rtl"
          size="50%">
        <el-table :data="playList">
          <el-table-column
              prop="1"
              label="歌曲名"
              style="width: 200px">
          </el-table-column>
          <el-table-column
              prop="2"
              label="艺人"
              style="width: 150px">
          </el-table-column>
          <el-table-column label="选择" style="width: 100%">
            <template slot-scope="scope">
              <el-button
                  size="mini"
                  @click="loadSrc(scope.row[0],scope.row[1]);setPlayingId(scope.row[0]);loadPic(scope.row[0]);">▶♫</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-drawer>
    </div>
    <div class="audio">
      <el-table :data="songs" style="width: 100%;height: 450px;overflow: auto" class="forTable">
        <el-table-column
            prop="name"
            label="歌曲名"
            style="width: 100%">
        </el-table-column>
        <el-table-column
            prop="artists[0].name"
            label="艺人"
            style="width: 100%">
        </el-table-column>
        <el-table-column label="选择" style="width: 100%">
          <template slot-scope="scope">
            <el-button
                size="mini"
                @click="addPlayList(scope.row.id,scope.row.name,scope.row.artists[0].name)">✚</el-button>
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

Vue.use(ElementUI);
Vue.use(VueAxios,axios);  //使用
var baseUrl = 'http://192.168.0.111:8074';
Vue.prototype.axios.defaults.baseURL=baseUrl;


export default {
  name: 'hostPage',
  props: {
    playList:{
      type:[],
      default:[]
    },
    showDrawer:{
      type:Boolean,
      default:false,
    },
    isNotShowingLyric:{
      type:Boolean,
      default:true
    },
    loadingHide:{
      default:true
    },
    logoHide: {
      default:false
    },
    picHide: {
      default:true
    },
    picUrl:null,
    currentTime:null,
    showingLyric:null,
    preSongs:null,
    audioSrc:null,
    searchText:null,
    searchUrl: {
      type: String,
      default: '/search'
    },
    songs:{
      type: JSON,
      default: null
    },
    data() {
      return {
        playingId:String,
        lyric:{
          type:String,
          default:""
        },
        lyricList:{
          type:[],
          default:[]
        },
        timeUpDatable:{
          type:Boolean,
          default:false
        }
      }
    },
  },
  methods:{
    setPlayingId(id){
      this.playingId = id;
    },
    playNext(){
      let id = this.playingId;
      for (let i = 0;i<this.playList.length;i++) {
        if(this.playList[i][0]==id){
          if (i!=this.playList.length-1){
            id = this.playList[i+1][0];
            this.playList.splice(i,1);
            console.log("下一首"+id);
            break;
          }
          else {
            id = null;
            this.playList.splice(i,1);
            console.log("下一首已经没了");
            break;
          }
        }
      }
      if (id!=null){
        this.loadSrc(id,name);
        this.loadPic(id);
      }
      this.playingId = id;
    },
    addPlayList(id,name,artist){
      let unit = [];
      unit.push(id);
      unit.push(name);
      unit.push(artist);
      this.playList.push(unit);
    },
    canplay(){
      this.loadingHide = true;
      this.picHide = false;
      this.$refs.audioRef.play();
    },
    loadNew(){
      this.picHide = true;
      this.loadingHide = false;
      this.showingLyric = "...歌词正在加载...";
    },
    loadPic(id){
      for (let songsKey in this.songs) {
        if (this.songs[songsKey].id==id){
          this.picUrl = this.songs[songsKey].album.blurPicUrl;
          this.logoHide = true;
          this.picHide = false;
        }
      }
    },

                //歌词格式转秒数函数,此函数不可用，待解决
    parseLyric(lyric) {
      this.lyricList = [];
      let lyrics = lyric.split('\n');
      let formedLyrics = [];
      for (let lyricsKey in lyrics) {
        if (lyrics[lyricsKey]!=""){
          formedLyrics.push(lyrics[lyricsKey]);
        }
      }
      for (let formedLyricsKey in formedLyrics) {
          let time = formedLyrics[formedLyricsKey].split(']')[0];
          time = time.replace(/\[/g,"");
          let min = time.split(":")[0];
          let sec = time.split(":")[1];
          time = Number(min*60)+ Number(sec);
          time = Math.trunc(time) ;
          let text = formedLyrics[formedLyricsKey].split(']')[1];
          let cube = [];
          cube.push(time);
          cube.push(text);
          this.lyricList.push(cube);
      }
      console.log(this.lyricList);
    },

          timeUpDate(){
            if(!this.timeUpDatable){
              return;
            }
            let currentTime = this.$refs.audioRef.currentTime;
            currentTime = Math.trunc(currentTime);
            for (let i = 0;i<this.lyricList.length;i++){
              if (currentTime == this.lyricList[i][0]){
                let showingLyric = "";
                showingLyric = showingLyric + this.lyricList[i][1] + '\n';
                if ((i+1)<this.lyricList.length){
                  showingLyric = showingLyric + this.lyricList[i+1][1] + '\n';
                }
                if ((i+2)<this.lyricList.length){
                  showingLyric = showingLyric + this.lyricList[i+2][1] + '\n';
                }
                if ((i+3)<this.lyricList.length){
                  showingLyric = showingLyric + this.lyricList[i+3][1] + '\n';
                }
                if ((i+4)<this.lyricList.length){
                  showingLyric = showingLyric + this.lyricList[i+4][1] + '\n';
                }
                this.showingLyric = showingLyric;
              }
            }
          },
         loadSrc(id,musicName){
              Vue.axios.get('/isOk?id='+encodeURIComponent(id)).then((response)=>{
                if (response.data.data=='no'){
                  alert("收费歌曲,无法播放");
                  return;
                }
              })
              this.audioSrc = baseUrl+"/music?id="+encodeURIComponent(id+".mp3")+"&musicName="+encodeURIComponent(musicName);
              Vue.axios.get('/lyric?id='+encodeURIComponent(id)).then((response)=>{
                if (response.data.data.nolyric){
                  this.$refs.audioRef.play();
                  return;
                }
                this.lyric = response.data.data.lyric;
                this.parseLyric(this.lyric);
                this.timeUpDatable = true;
                this.isNotShowingLyric = false;
              })
         },
        searchMethod(){
          Vue.axios.post(this.searchUrl, {//发送请求 跳转页面
            s: this.searchText,
            offset: '0',
            limit: '100',
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
.pic{
  align-content: center;
  height: 300px;
  width: 220px;
  border-radius:40%;
  position: relative;
}
.forTable{
  align: center;
  text-align: right;
  align-content: center;
}
.audio {
  font-size: 18px;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: gray;
  margin-top: 60px;
  overflow: hidden;
}
</style>
