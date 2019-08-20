<template>
  <transition appear name="slide">
    <music-list :title="title" :bg-image="bgImage" :songs="songs"></music-list>
  </transition>
</template>

<script>
  import { mapGetters } from 'vuex'
  import { getSingerDetail } from '../../api/singer'
  import { ERR_OK } from '../../api/config'
  import { createSong, isValidMusic } from '../../assets/js/song'
  import MusicList from '../music-list/music-list'

  export default {
    name: 'singer-detail',
    computed: {
      ...mapGetters([
        'singer'
      ]),
      title () {
        return this.singer.name
      },
      bgImage () {
        return this.singer.avatar
      }
    },
    data () {
      return {
        songs: []
      }
    },
    created () {
      this._getDetail()
    },
    methods: {
      _getDetail () {
        if (!this.singer.id) {
          this.$router.push('/singer')
          return
        }
        getSingerDetail(this.singer.id).then((res) => {
          if (res.code === ERR_OK) {
            this.songs = this._normalizeSongs(res.data.list)
            console.log(this.songs)
          }
        })
      },
      _normalizeSongs (list) {
        let ret = []
        list.forEach((item) => {
          let { musicData } = item
          if (isValidMusic(musicData)) {
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

<style scoped lang="stylus" rel="stylesheet/stylus">
  .slide-enter-active, .slide-leave-active
    transition all 0.3s

  .slide-enter, .slide-leave-to
    transform translate3d(100%, 0, 0)
</style>
