<template>
    <div class="player">
      <audio id="p1"></audio>
      <div class="disc">
          <transition>
              <img :src="item.poster" :alt="item.name" :style="{transform:'rotate('+(current/duation*360*2)+'deg)'}" />
            </transition>
      </div>
      <h2 class="title">{{item.name}}</h2>
      <h3 class="artist">{{item.artist}}</h3>
      <div id="lyric" class="lyric">
        <p class="previous" v-for="(item,index) in lines" :key="index" :class="currentLine==index?currentClass:''">
          {{item.text}}
        </p>
      </div>
      <div class="controls">
          <button class="active" @click="prev()"><i class="fa fa-backward"></i></button>
          <button class="active" @click="play()"><i class="fa" :class="!item.playing?'fa-play':'fa-pause'"></i></button>
          <button class="active" @click="next()"><i class="fa fa-forward"></i></button>
      </div>
    </div>
</template>
<script>
import '../assets/css/app.css'
import '../assets/css/font-awesome.css'
import '../assets/css/normalize.css'
// import $ from 'jquery'
export default{
  name: 'player',
  data () {
    return {
      item: {}, // 当前要播放的音乐对象
      lines: [], // 歌词存放的数组
      currentLine: 'w', // 当前播放歌词行,不放0
      currentClass: 'current', // 当前被选中的样式
      lrccurrent: 0,
      current: '', // 当前播放时间
      currentindex: 0, // 用来记录播放音乐下标
      duation: 0// 总时长
    }
  },
  mounted () {
    var that = this
    var id = this.$route.params.id// 取出URL的参数
    // 根据id去vuex中匹配要播放的数据
    this.$store.state.musiclist.forEach((a, index) => {
      if (a._id === id) {
        that.item = a
        that.init()
      }
    })
  },
  methods: {
    init: function () {
      this.fetch()// 调用分割歌词方法
      this.item.playing = false// 默认不播放
      document.getElementById('p1').src = this.item.filename
      document.getElementById('p1').addEventListener('timeupdate', () => {
        this.current = document.getElementById('p1').currentTime
        var oldtime = this.lines[this.lrccurrent].time.split(':')
        var lrctime = parseInt(oldtime[0]) * 60 + parseInt(oldtime[1])
        var str1 = this.current - lrctime// 当前播放的时间 - 歌词时间
        if (str1 > 0 || str1 === 0) {
          this.currentLine = this.lrccurrent// 改变歌词的显示
          this.lrccurrent += 1// 下一个歌词的下标
        }
      })
    },
    fetch: function () { // 分割歌词
      this.lines = []
      var lines = this.item.lrc.split('\n')// 将歌词按照\n分割成数组
      for (var k in lines) { // 遍历每一句歌词，获取除时间外的歌词内容
        var timeMatch = lines[k].match(/\[(\d+:\d+\.\d+)\]/)
        this.lines.push({
          time: timeMatch ? timeMatch.pop() : '',
          text: lines[k].replace(/^.+?\]/, '')
        })
      }
    },
    play: function () {
      if (this.item.playing) {
        document.getElementById('p1').pause()
      } else {
        document.getElementById('p1').play()
        this.duation = document.getElementById('p1').duration
      }
      this.item.playing = !this.item.playing
    },
    prev: function () {
      var number = --this.currentindex
      if (number < 0) {
        number = 0
        this.currentindex = number
      } else {}
      this.item = this.$store.state.musiclist[number]
      this.current = 0
      this.item.playing = true
      this.init()
    },
    next: function () {
      var number = ++this.currentindex
      if (this.$store.state.musiclist.length - 1 < number) {
        number = this.$store.state.musiclist.length - 1
        this.currentindex = number
      } else {}
      this.item = this.$store.state.musiclist[number]
      this.current = 0
      this.item.playing = true
      this.init()
    }
  }
}
</script>
<style>
</style>
