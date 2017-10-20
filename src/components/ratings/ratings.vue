<template>
  <div class="ratings" ref='ratingContainer'>
    <div class="rating-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class='desc'>综合评分</div>
          <div class="text">高于周边{{seller.rankRate}}</div>
        </div>
        <div class="overview-right">
          <div class="star-wrapper">
            <span class="title">服务态度</span>
            <star class="star" :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="star-wrapper">
            <span class="title">服务态度</span>
            <star class="star" :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="deliveryTime-wrapper">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect @select='setSelectType' @isonlycontent='toggleContent' :selecttype='selecttype' :ratings='ratings'
                    :onlyContent='onlyContent'></ratingselect>
      <div class="ratings-wrapper">
        <ul>
          <li v-show="needShow(rating.rateType,rating.text)" class="rating-item border-1px" v-for='(rating,index) in ratings' :key='index'>
            <div class="avatar">
              <img :src="rating.avatar" width='28px' height='28px'>
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star class="star" :size='24' :score="rating.score"></star>
                <span class="deliveryTime" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text" v-show="rating.text">{{rating.text}}</p>
              <div class="recommend-wrapper" v-show='rating.recommend&&rating.recommend.length'>
                <i class="icon-thumb_up"></i>
                <span class="recommend" v-for="(item,I) in rating.recommend" :key='I'>{{item}}</span>
              </div>
              <div class="time">
                {{rating.rateTime | formatData}}
              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import {formatDate} from 'common/js/data'
  import BSscroll from 'better-scroll'
  import star from 'components/star/star'
  import split from 'components/split/split'
  import ratingselect from 'components/ratingselect/ratingselect'

  const ALL = 2
  const ERR_OK = 0
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        onlyContent: true,
        selecttype: ALL
      }
    },
    created() {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.ratings = response.data
          this.$nextTick(() => {
            this._initScroll()
          })
        }
      })
    },
    filters: {
      formatData(time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    methods: {
      _initScroll() {
        this.ratingScroll = new BSscroll(this.$refs.ratingContainer, {
          click: true
        })
      },
      setSelectType(type) {
        this.selecttype = type
        this.$nextTick(() => {
          this.ratingScroll.refresh()
        })
      },
      toggleContent() {
        this.onlyContent = !this.onlyContent
        this.$nextTick(() => {
          this.ratingScroll.refresh()
        })
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false
        }
        if (this.selecttype === ALL) {
          return true
        } else {
          return type === this.selecttype
        }
      }
    },
    components: {
      star, split, ratingselect
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding 18px 0
      .overview-left
        flex: 0 0 138px
        padding 6px 0
        text-align center
        box-sizing border-box
        border-right 1px solid rgba(7, 17, 27, .1)
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
          width: 120px
        .score
          margin-bottom 6px
          font-size 24px
          line-height: 28px
          color: rgb(255, 153, 0)
        .desc
          margin-bottom 8px
          font-size 12px
          line-height: 12px
          color: rgb(7, 17, 27)
        .text
          font-size: 10px
          line-height: 10px
          color: rgb(147, 153, 159)
      .overview-right
        flex: 1
        padding 6px 0 6px 24px
        @media only screen and (max-width: 320px)
          padding-left: 6px
        .star-wrapper
          margin-bottom 8px
          font-style: 0
          .title
            display inline-block
            margin-right 12px
            vertical-align topd
            line-height: 18px
            font-size: 12px
            color: rgb(7, 17, 27)
          .star
            display inline-block
            margin-right 12px
            vertical-align top
            line-height: 18px
          .score
            display inline-block
            vertical-align top
            line-height: 18px
            font-size: 12px
            color: rgb(255, 153, 0)

        .deliveryTime-wrapper
          .title
            display inline-block
            margin-right 12px
            vertical-align top
            line-height: 18px
            font-size: 12px
            color: rgb(7, 17, 27)
          .time
            vertical-align top
            line-height: 18px
            font-size: 12px
            color: rgb(147, 153, 159)

    .ratings-wrapper
      padding 0 18px
      .rating-item
        display flex
        padding 18px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex 0 0 28px
          width 28px
          margin-right 12px
          img
            border-radius 50%
        .content
          flex 1
          position relative
          .name
            margin-bottom 4px
            line-height: 12px
            font-size 10px
            color rgb(7, 17, 27)
          .star-wrapper
            margin-bottom 6px
            font-size 0
            .star
              display inline-block
              vertical-align: top
              margin-right 6px
            .deliveryTime
              display inline-block
              vertical-align: top
              line-height: 12px
              font-size 10px
              color: rgb(147, 153, 159)
          .text
            margin-bottom 8px
            line-height: 18px
            font-size 12px
            color rgb(7, 17, 27)
          .recommend-wrapper
            line-height: 16px
            font-size 0
            .icon-thumb_up
              display inline-block
              margin-right 8px
              margin-bottom 4px
              vertical-align: top
              font-size 10px
              color rgb(0, 160, 220)
            .recommend
              display inline-block
              vertical-align: top
              padding 0 6px
              margin-right 8px
              margin-bottom 4px
              box-sizing border-box
              border 1px solid rgba(7,17,27,.1)
              background-color #fff
              border-radius 1px
              font-size 9px
          .time
            position absolute
            top: 0
            right: 0
            line-height: 12px
            font-size 10px
            color rgb(147, 153, 159)
</style>
