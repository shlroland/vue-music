<template>
  <div class="singer" ref="singer">
    <listview ref="list" @select="selectSinger" :data="singers"></listview>
    <router-view></router-view>
  </div>
</template>

<script>
  import { ERR_OK } from '../../api/config'
  import { getSingerList } from '../../api/singer'
  import Singer from 'assets/js/singer'
  import Listview from 'base/listview/listview'
  import { mapMutations } from 'vuex'
  import { playlistMixin } from '../../assets/js/mixin'

  const HOT_SINGER_LEN = 10
  const HOT_NAME = '热门'

  export default {
    name: 'singer',
    mixins: [playlistMixin],
    components: {
      Listview
    },
    data () {
      return {
        singers: []
      }
    },
    created () {
      this._getSingerList()
    },
    methods: {
      ...mapMutations({
        setSinger: 'SET_SINGER'
      }),
      handlePlayList (playlist) {
        this.$refs.singer.style.bottom = playlist.length > 0 ? '60px' : ''
        this.$refs.list.refresh()
      },
      selectSinger (singer) {
        console.log(singer)
        this.$router.push({
          path: `/singer/${singer.id}`
        })
        this.setSinger(singer)
      },
      _getSingerList () {
        getSingerList().then(res => {
          if (res.code === ERR_OK) {
            this.singers = this._normalizeSinger(res.data.list)
            console.log(this.singers)
          }
        })
      },
      _normalizeSinger (list) {
        let map = {
          hot: {
            tilte: HOT_NAME,
            items: []
          }
        }
        list.forEach((item, index) => {
          if (index < HOT_SINGER_LEN) {
            map.hot.items.push(new Singer({
              name: item.Fsinger_name,
              id: item.Fsinger_mid
            }))
          }
          const key = item.Findex
          if (!map[key]) {
            map[key] = {
              tilte: key,
              items: []
            }
          }
          map[key].items.push(new Singer({
            name: item.Fsinger_name,
            id: item.Fsinger_mid
          }))
        })
        let ret = []
        let hot = []
        for (let key in map) {
          let val = map[key]
          if (val.tilte.match(/[a-zA-Z]/)) {
            ret.push(val)
          } else if (val.tilte === HOT_NAME) {
            hot.push(val)
          }
        }
        ret.sort((a, b) => {
          return a.tilte.charCodeAt(0) - b.tilte.charCodeAt(0)
        })
        return hot.concat(ret)
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .singer
    position: fixed
    top: 88px
    bottom: 0
    width: 100%
</style>
