<template>
  <div
    class="suggest"
    v-loading:[loadingText]="loading"
    v-no-result:[noResultText]="noResult"
  >
    <ul class="suggest-list">
      <li class="suggest-item" v-if="singer">
        <div class="icon">
          <i class="icon-mine" />
        </div>
        <div class="name">
          <p class="text">{{ singer.name }}</p>
        </div>
      </li>
      <li class="suggest-item" v-for="song in songs" :key="song.id">
        <div class="icon">
          <i class="icon-music" />
        </div>
        <div class="name">
          <p class="text">{{ song.singer }}-{{ song.name }}</p>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { computed, ref/* , watch */ } from 'vue'
// import { search } from '../../service/search'
// import { processSongs } from '../../service/song'
export default {
  name: 'suggest',
  props: {
    query: String,
    showSinger: {
      type: Boolean,
      default: true
    }
  },
  setup (props) {
    const singer = ref(null)
    const songs = ref([])
    const hasMore = ref(true)
    // const page = ref(1)
    const loadingText = ref('')
    const noResultText = ref('抱歉，暂无搜索结果')

    const loading = computed(() => !singer.value && !songs.value.length)
    const noResult = computed(() => !singer.value && !songs.value.length && !hasMore.value)
    // const queryContent = computed(() => props.query.trim())
    // watch(queryContent, async (newQuery) => { // ! watch只能监听响应式数据，而props里的属性只是String或者Boolean(对于此),所以需要换成返回该值得函数
    //   if (!newQuery) {
    //     return
    //   }
    //   await searchFirst()
    // })

    // async function searchFirst () {
    //   if (!queryContent.value) {
    //     return
    //   }
    //   // #每次搜索前需要将内容重置
    //   page.value = 1
    //   songs.value = []
    //   singer.value = null
    //   hasMore.value = true
    //   let result
    //   search(queryContent.value, page.value, props.showSinger).then(res => {
    //     console.log('suggest: ', res)
    //     result = res
    //   }).catch(err => console.error(err))

    //   songs.value = await processSongs(result.songs)
    //   singer.value = result.singer
    //   hasMore.value = result.hasMore
    // }

    return {
      singer,
      songs,
      loading,
      noResult,
      loadingText,
      noResultText
    }
  }
}
</script>

<style lang="scss" scoped>
.suggest {
  height: 100%;
  overflow: hidden;
  .suggest-list {
    padding: 0 30px;
    .suggest-item {
      display: flex;
      align-items: center;
      padding-bottom: 20px;
      .icon {
        flex: 0 0 30px;
        width: 30px;
        [class^='icon-'] {
          font-size: 14px;
          color: $color-text-d;
        }
      }
      .name {
        flex: 1;
        font-size: $font-size-medium;
        color: $color-text-d;
        overflow: hidden;
        .text {
          @include no-wrap();
        }
      }
    }
  }
}
</style>
