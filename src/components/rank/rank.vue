<template>
  <div class="rank" ref="rank">
    <scroll ref="topList" :data="topList" class="toplist">
      <ul>
        <li @click="selectItem(item)" class="item" v-for="item in topList">
          <div class="icon">
            <img width="100" height="100" v-lazy="item.picUrl">
          </div>
          <ul class="songlist">
            <li class="song" v-for="(song, index) in item.songList">
              <span>{{index + 1}}</span>
              <span>{{song.songname}}</span>
            </li>
          </ul>
        </li>
      </ul>
      <div class="loading-container" v-show="!topList.length">
        <loading></loading>
      </div>
    </scroll>
    <router-view></router-view>
  </div>
</template>

<script>
  import { getTopList } from '../../api/rank'
  import { ERR_OK } from '../../api/config'
  import Scroll from 'base/scroll/scroll'
  import Loading from 'base/loading/loading'
  import { playlistMixin } from '../../assets/js/mixin'
  import { mapMutations } from 'vuex'

  export default {
    name: 'rank',
    mixins: [playlistMixin],
    created () {
      this._getTopList()
    },
    data () {
      return {
        topList: []
      }
    },
    methods: {
      _getTopList () {
        getTopList().then((res) => {
          getTopList().then((res) => {
            if (res.code === ERR_OK) {
              this.topList = res.data.topList
            }
          })
        })
      },
      handlePlayList (playlist) {
        this.$refs.rank.style.bottom = playlist.length > 0 ? '60px' : ''
        this.$refs.topList.refresh()
      },
      selectItem (item) {
        this.$router.push({
          path: `/rank/${item.id}`
        })
        this.setTopList(item)
      },
      ...mapMutations({
        setTopList: 'SET_TOP_LIST'
      })
    },
    components: {
      Scroll,
      Loading
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~assets/stylus/variable"
  @import "~assets/stylus/mixin"

  .rank
    position: fixed
    width: 100%
    top: 88px
    bottom: 0

    .toplist
      height: 100%
      overflow: hidden

      .item
        display: flex
        margin: 0 20px
        padding-top: 20px
        height: 100px

        &:last-child
          padding-bottom: 20px

        .icon
          flex: 0 0 100px
          width: 100px
          height: 100px

        .songlist
          flex: 1
          display: flex
          flex-direction: column
          justify-content: center
          padding: 0 20px
          height: 100px
          overflow: hidden
          background: $color-highlight-background
          color: $color-text-d
          font-size: $font-size-small

          .song
            no-wrap()
            line-height: 26px

      .loading-container
        position: absolute
        width: 100%
        top: 50%
        transform: translateY(-50%)
</style>
