<template>
  <transition name="slide">
    <music-list :title="title" :bgImg="bgImg"></music-list>
  </transition>
</template>
<script>
  import MusicList from 'components/music-list/music-list'
  import { mapGetters } from 'vuex'

  export default {
    computed: {
      title () {
        return this.disc.dissname
      },
      bgImg () {
        return this.disc.imgurl
      },
      ...mapGetters([
        'disc'
      ])
    },
    components: {
      MusicList
    }
  }
</script>
<style scoped lang="stylus" rel="stylesheet/stylus">
  .slide-entry-active, .slide-leave-active
    transition: all 0.3s

  .slide-entry, .slide-leave-to
    transform: translate3d(100%, 0, 0)
</style>
