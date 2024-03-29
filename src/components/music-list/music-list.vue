<template>
  <div class="music-list">
    <div class="back" @click="goBack">
      <i class="icon-back"></i>
    </div>
    <h1 class="title">{{ title }}</h1>
    <div class="bg-image" :style="bgImageStyle" ref="bgImg">
      <div class="filter" />
      <div class="play-btn-wrapper" v-show="showBtn">
        <div v-show="songs.length > 0" class="play-btn" @click="random">
          <i class="icon-play" />
          <span class="text">随机播放全部</span>
        </div>
      </div>
    </div>
    <Scroll
      class="list"
      :style="scrollStyle"
      v-loading="loading"
      :probe-type="3"
      @scroll="onScroll"
      v-no-result="noResult"
    >
      <div class="song-list-wrapper">
        <SongList :songs="songs" @select="selectItem" :rank="rank" />
      </div>
    </Scroll>
  </div>
</template>

<script>
import Scroll from '../wrap-scroll/wrap-scroll.js'
import SongList from '../base/song-list/song-list.vue'
import { mapActions, mapState } from 'vuex'
const TITLE_HEIGHT = 40

export default {
  name: 'music-list',
  components: {
    SongList,
    Scroll
  },
  props: {
    songs: {
      type: Array,
      default () {
        return []
      }
    },
    title: String,
    pic: String,
    loading: Boolean,
    rank: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      imgHeight: 0,
      scrollY: 0,
      maxTranslateY: 0
    }
  },
  computed: {
    showBtn () {
      let show = true
      if (this.scrollY >= this.maxTranslateY) {
        show = false
      }
      return show
    },
    bgImageStyle () {
      const scrollY = this.scrollY
      let zIndex = 0
      let height = 0
      let paddingTop = '70%'
      let scale = 1
      if (scrollY > this.maxTranslateY) {
        zIndex = 10
        paddingTop = 0
        height = `${TITLE_HEIGHT}px`
        // this.showBtn = false
      }
      if (scrollY < 0) { // 由于多次使用this.scrollY,考虑到性能优化，应该在前面用变量缓存
        scale = 1 + Math.abs(scrollY / this.imgHeight)
      }
      return {
        backgroundImage: `url(${this.pic})`,
        zIndex,
        height,
        paddingTop,
        transform: `scale(${scale})`
      }
    },
    scrollStyle () {
      const bottom = this.playlist.length ? '60px' : ''
      return {
        top: `${this.imgHeight}px`,
        bottom
      }
    },
    noResult () {
      return !this.loading && !this.songs.length
    },
    ...mapState([
      'playlist'
    ])
  },
  mounted () {
    this.imgHeight = this.$refs.bgImg.offsetHeight
    this.maxTranslateY = this.imgHeight - TITLE_HEIGHT
  },
  methods: {
    goBack () {
      this.$router.back()
    },
    onScroll (pos) {
      this.scrollY = -pos.y
    },
    selectItem ({ song, index }) {
      this.selectPlay({
        list: this.songs,
        index
      })
    },
    random () {
      this.randomPlay({
        list: this.songs
      })
    },
    ...mapActions([
      'selectPlay',
      'randomPlay'
    ])
  }
}
</script>

<style lang="scss" scoped>
.music-list {
  position: relative;
  height: 100%;
  .back {
    position: absolute;
    top: 0;
    left: 6px;
    z-index: 20;
    transform: translateZ(2px); /* 适配苹果手机z-index不生效 */
    .icon-back {
      display: block;
      padding: 10px;
      font-size: $font-size-large-x;
      color: $color-theme;
    }
  }
  .title {
    position: absolute;
    top: 0;
    left: 10%;
    width: 80%;
    z-index: 20;
    transform: translateZ(2px);
    @include no-wrap();
    text-align: center;
    line-height: 40px;
    font-size: $font-size-large;
    color: $color-text;
  }
  .bg-image {
    position: relative;
    width: 100%;
    transform-origin: top;
    background-size: cover;
    // z-index: 10;
    .play-btn-wrapper {
      position: absolute;
      bottom: 20px;
      z-index: 10;
      width: 100%;
      .play-btn {
        box-sizing: border-box;
        width: 135px;
        padding: 7px 0;
        margin: 0 auto;
        text-align: center;
        border: 1px solid $color-theme;
        color: $color-theme;
        border-radius: 100px;
        font-size: 0;
      }
      .icon-play {
        display: inline-block;
        vertical-align: middle;
        margin-right: 6px;
        font-size: $font-size-medium-x;
      }
      .text {
        display: inline-block;
        vertical-align: middle;
        font-size: $font-size-small;
      }
    }
    .filter {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(7, 17, 27, 0.4);
    }
  }
  .list {
    position: absolute;
    bottom: 0;
    width: 100%;
    z-index: 0;
    .song-list-wrapper {
      padding: 20px 30px;
      background: $color-background;
    }
  }
}
</style>
