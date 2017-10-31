<template lang="html">
  <transition name="slide">
    <music-list :songs="songs" :bg-img="bgImg" :title="title"></music-list>
  </transition>
</template>

<script>
import {
  mapGetters
} from 'vuex'
import {
  getDetail
} from 'api/singer'
import {
  ERR_OK
} from 'api/config'
import {
  createSong
} from 'common/js/song'
import MusicList from 'components/music-list/music-list'

export default {
  computed: {
    bgImg() {
      return this.singer.avatar
    },
    title() {
      return this.singer.name
    },
    ...mapGetters([
      'singer'
    ])
  },
  data() {
    return {
      songs: []
    }
  },
  created() {
    this._getDetail()
  },
  methods: {
    _getDetail() {
      if (!this.singer.id) {
        this.$router.push('/singer')
        return
      }
      return getDetail(this.singer.id).then((res) => {
        if (res.code === ERR_OK) {
          this.songs = this._normalistSongs(res.data.list)
        }
      })
    },
    _normalistSongs(list) {
      let ret = []
      list.forEach((item) => {
        // 解构赋值
        let {
          musicData
        } = item
        if (musicData.songid && musicData.albummid) {
          ret.push(createSong(musicData))
        }
      })
      return ret
    }
  },
  components: {
    MusicList
  }

}
</script>

<style lang="stylus" rel="stylusheet/stylus">
  @import "~common/stylus/variable"
  .slide-enter-active, .slide-leave-active
    transition: all 0.3s
  .slide-enter, .slide-leave-to
    transform: translate3d(100%, 0, 0)
</style>
