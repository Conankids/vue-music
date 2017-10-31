<template lang="html">
  <div class="singer">
    <listview  @select="selectSinger" :data="singers"></listview>
    <router-view></router-view>
  </div>
</template>

<script>
import {
  getSingerList
} from 'api/singer'
import {
  ERR_OK
} from 'api/config'
import Singer from 'common/js/singer'
import listview from 'base/listview/listview'
import {mapMutations} from 'vuex'

const HOT_NAME = '热门'
const HOT_SINGER_NUM = 10

export default {
  data() {
    return {
      singers: []
    }
  },
  created() {
    setTimeout(() => {
      this._getSingerList()
    }, 20)
  },
  methods: {
    selectSinger(singer) {
      this.$router.push({
        path: `/singer/${singer.id}`
      })
      this.setSinger(singer)
    },
    _getSingerList() {
      getSingerList().then((res) => {
        if (res.code === ERR_OK) {
          this.singers = this._normalistSinger(res.data.list)
        }
      })
    },
    _normalistSinger(list) {
      let map = {
        hot: {
          title: HOT_NAME,
          items: []
        }
      }
      list.forEach((item, index) => {
        if (index < HOT_SINGER_NUM) {
          map.hot.items.push(new Singer({
            id: item.Fsinger_mid,
            name: item.Fsinger_name
          }))
        }
        const key = item.Findex
        if (!map[key]) {
          map[key] = {
            title: key,
            items: []
          }
        }
        map[key].items.push(new Singer({
          id: item.Fsinger_mid,
          name: item.Fsinger_name
        }))
      })
      // 为了得到有序列表，需处理一下map
      let hot = []
      let ret = []
      for (let key in map) {
        let val = map[key]
        if (val.title.match(/[a-zA-Z]/)) {
          ret.push(val)
        } else if (val.title === HOT_NAME) {
          hot.push(val)
        }
      }
      ret.sort((a, b) => {
        return a.title.charCodeAt(0) - b.title.charCodeAt(0)
      })
      return hot.concat(ret)
    },
    ...mapMutations({
      // vuex保存状态
      setSinger: 'SET_SINGER'
    })
  },
  components: {
    listview
  }
}
</script>

<style scoped lang="stylus" rel="stylusheet/stylus">
  .singer
    position: fixed
    top: 88px
    bottom: 0
    width: 100%
</style>
