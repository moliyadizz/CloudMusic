<template>
  <div class="discovery-container">
    <!-- 轮播图 -->
    <el-carousel class="" :interval="4000" type="card">
      <!-- 循环获取到的接口数据 -->
      <el-carousel-item v-for="(item, index) in banners" :key="index">
        <img :src="item.imageUrl" alt="" />
      </el-carousel-item>
    </el-carousel>
    <!-- 推荐歌单 -->
    <div class="recommend">
      <h3 class="title">
        推荐歌单
      </h3>
      <div class="items">
        <div class="item" v-for="(item, index) in list" :key="index" @click="toPlaylist(item.id)">
          <div class="img-wrap">
            <div class="desc-wrap">
              <span class="desc">播放量:{{ item.playCount }}</span>
            </div>
            <img :src="item.picUrl" alt="" />
          </div>
          <p class="name">{{ item.name }}</p>
        </div>
      </div>
    </div>
    <!-- 每日推荐 -->
    <div class="news">
      <h3 class="title">
        每日推荐
      </h3>
      <div class="items">
        <div class="item" v-for="(item, index) in songs.slice(0, 10)" :key="index">
          <div class="img-wrap">
            <!-- 封面 -->
            <img :src="item.al.picUrl" alt="" />
            <span @click="playMusic(item.id)" class="iconfont icon-play"></span>
          </div>
          <div class="song-wrap">
            <!-- 歌名 -->
            <div class="song-name">{{ item.al.name }}</div>
            <!-- 歌手名 -->
            <div class="singer">{{ item.ar[0].name }}</div>
          </div>
        </div>
      </div>
    </div>
    <!-- 推荐MV -->
    <div class="mvs">
      <h3 class="title">推荐MV</h3>
      <div class="items">
        <div class="item" v-for="(item,index) in mvs" :key="index" @click="toMV(item.id)">
          <div class="img-wrap">
            <img :src="item.picUrl" alt="" />
            <span class="iconfont icon-play"></span>
            <div class="num-wrap">
              <div class="iconfont icon-play"></div>
              <!-- 播放次数 -->
              <div class="num">{{ item.playCount }}</div>
            </div>
          </div>
          <div class="info-wrap">
            <!-- mv名 -->
            <div class="name">{{ item.name }}</div>
            <!-- 歌手名 -->
            <div class="singer">{{ item.artistName }}</div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
  // 导入 axios
  import axios from 'axios'
  export default {
    name: 'discovery',
    data() {
      return {
        // 轮播图
        banners: [],
        // 推荐歌单
        list: [],
        // 最新音乐
        songs: [],
        // 推荐mv
        mvs:[]
      }
    },
    created() {
      // console.log('created')
      // 轮播图接口
      axios({
        url: 'http://localhost:3000/banner',
        method: 'get'
      }).then(res => {
        //console.log(res)
        this.banners = res.data.banners
      })

      // 推荐歌单
      axios({
        url: 'http://localhost:3000/personalized',
        method: 'get',
        params: {
          // 获取的数据量
          limit: 10
        }
      }).then(res => {
        //console.log(res)
        this.list = res.data.result
      })

      // 每日推荐
      axios({
        url: 'http://localhost:3000/recommend/songs',
        method: 'get',
      }).then(res => {
        //console.log(res)
        this.songs = res.data.data.dailySongs
      })

      // 推荐mv
      axios({
        url: 'http://localhost:3000/personalized/mv',
        method: 'get'
      }).then(res => {
        //console.log(res)
        this.mvs = res.data.result
      })
    },
    methods: {
      playMusic(id) {
        // console.log(id)
        axios({
          url: 'http://localhost:3000/song/url',
          method: 'get',
          params: {
            id // id:id
          }
        }).then(res => {
          console.log(res)
          let url = res.data.data[0].url
          // console.log(this.$parent.musicUrl)
          // 设置给父组件的 播放地址
          this.$parent.musicUrl = url
        })
      },
      // 去歌单详情页
      toPlaylist(id){
        // 跳转并携带数据即可
        this.$router.push(`/playlist?q=${id}`)
      },
      // 去mv详情页
      toMV(id){
        this.$router.push(`/mv?q=${id}`)
      }
    }
  }
</script>

<style></style>
