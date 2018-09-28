<template>
    <div class="list">
        <ol>
            <li v-for='(item,index) in list' :key="index">
                <a @click='play(item._id)'>
                    <div class="info">
                        <h3 class="title">{{item.name}}</h3>
                        <span class="artist">{{item.artist}}</span>
                    </div>
                    <span class="duration">{{item.duration}}</span>
                    <div class="photo"><img :src="item.poster" alt=""></div>
                </a>
            </li>
        </ol>
    </div>
</template>
<script>
import '../assets/css/app.css'
import '../assets/css/font-awesome.css'
import '../assets/css/normalize.css'
import axios from 'axios'
export default{
  name: 'list',
  data () {
    return {
      list: []
    }
  },
  mounted () {
    // 查询所有的音乐列表
    axios.get('http://localhost:3000/all').then((va) => {
      // 给仓库初始化数据
      this.$store.commit('INITMUSIC', va.data)
      // 给当前页面赋值
      this.list = va.data
    })
  },
  methods: {
    play: function (id) {
      // 点击播放的音频
      this.$router.push('/item/' + id)
    }
  }
}
</script>
<style>
</style>
