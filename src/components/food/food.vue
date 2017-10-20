<template>
  <transition name="slideAnimation">
    <div class="food-Wraper" v-show="showFlag" ref='foodScroll'>
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
        </div>
        <div class="back" @click='hide'>
          <i class='icon-arrow_lift'></i>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now-price">￥{{food.price}}</span>
            <span v-show="food.oldPrice>0" class="old-price">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper" v-show='food.count>0'>
            <cartcontrol @add='addFood' :food="food"></cartcontrol>
          </div>
          <transition name='fade'>
            <div @click.stop.prevent='addFirst' class="buy" v-show="!food.count||food.count=== 0">加入购物车</div>
          </transition>
        </div>
        <split v-show='food.info'></split>
        <div class="info" v-show='food.info'>
          <h1 class='title'>商品信息</h1>
          <p class='text'>{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingselect @select='setSelectType' @isonlycontent='toggleContent' :desc='desc' :ratings="food.ratings" :onlyContent='onlyContent' :selecttype='selecttype'></ratingselect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings&&food.ratings.length">
              <li v-show="needShow(rating.rateType,rating.text)" class="rating-item" v-for="(rating,index) in food.ratings" :key="index">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" class="avatar" width="12px" height="12px">
                </div>
                <div class="time">{{rating.rateTime|formatData}}</div>
                <p class="rating-content">
                  <i :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></i>
                  <span class="text">{{rating.text}}</span>
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings||!food.ratings.length">暂无评价</div>
          </div>
        </div>

      </div>
    </div>
  </transition>

</template>

<script type="text/ecmascript-6">
    import cartcontrol from 'components/cartcontrol/cartcontrol'
    import BSscroll from 'better-scroll'
    import Vue from 'vue'
    import split from 'components/split/split'
    import {formatDate} from 'common/js/data'
    import ratingselect from 'components/ratingselect/ratingselect'
    const ALL = 2
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
        desc: {
          positive: '推荐',
          negative: '吐槽',
          all: '全部'
        },
        onlyContent: true,
        selecttype: ALL
      }
    },
    filters: {
      formatData(time) {
        let date = new Date(time)
        return formatDate(date, 'yyyy-MM-dd hh:mm')
      }
    },
    methods: {
      show() {
        this.showFlag = true
        this.selecttype = ALL
        this.onlyContent = true
        this.$nextTick(() => {
          if (!this.foodScroll) {
            this.foodScroll = new BSscroll(this.$refs.foodScroll, {
              click: true
            })
          } else {
            this.foodScroll.refresh()
          }
        })
      },
      hide() {
        this.showFlag = false
      },
      addFirst(event) {
        if (!event._constructed) {
          return
        }
        this.$emit('add', event.target)
        Vue.set(this.food, 'count', 1)
      },
      addFood(target) {
        this.$emit('add', target)
      },
      setSelectType(type) {
        this.selecttype = type
        this.$nextTick(() => {
          this.foodScroll.refresh()
        })
      },
      toggleContent() {
        this.onlyContent = !this.onlyContent
        this.$nextTick(() => {
          this.foodScroll.refresh()
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
      cartcontrol,
      split,
      ratingselect
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .food-Wraper
    position: fixed
    top: 0
    left: 0
    bottom: 48px
    width: 100%
    z-index: 20
    background-color: #fff
    &.slideAnimation-enter-active, &.slideAnimation-leave-active
      transition all .2s linear
    &.slideAnimation-enter-to, &.slideAnimation-leave
      transform translate3d(0, 0, 0)
    &.slideAnimation-enter, &.slideAnimation-leave-to
      transform translate3d(100%, 0, 0)
    .image-header
      position: relative
      top: 0
      left: 0
      width: 100%
      height: 0
      padding-top: 100%
      img
        position absolute
        top: 0
        left: 0
        width 100%
        height 100%
    .back
      position absolute
      top: 10px
      left: 0
      .icon-arrow_lift
        display block
        font-size 20px
        color #fff
        padding 10px
    .content
      position relative
      padding: 18px
      .title
        margin-bottom: 8px
        font-size: 14px
        font-weight 700
        line-height: 14px
        color: rgb(7, 17, 27)
      .detail
        line-height: 10px
        margin-bottom 18px
        font-size 0px
        .sell-count, .rating
          font-size 10px
          color rgb(147, 153, 159)
        .rating
          margin-left 12px

      .price
        font-size: 0
        line-height: 24px
        font-weight: 700
        .now-price
          margin-right: 8px
          font-size: 14px
          font-weight: 700
          color: rgb(240, 20, 20)
        .old-price
          font-size: 10px
          font-weight: 700
          text-decoration: line-through
          color: rgb(147, 153, 159)


      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        z-index: 10
        height: 24px
        line-height: 24px
        padding: 0 12px
        box-sizing: border-box
        border-radius: 12px
        font-size: 10px
        color: #fff
        background: rgb(0, 160, 220)
        &.fade-enter-active, &.fade-leave-active
          transition opacity .4s
        &.fade-enter-to, &.fade-leave
          opacity 1
        &.fade-enter, &.fade-leave-to
          opacity 0

    .info
      padding 18px
      font-size 14px
      line-height: 14px
      color: rgb(7,17,27)
      .title
        margin-bottom 6px
      .text
        padding 0 8px
        font-size 12px
        line-height: 24px
        color: rgb(77,85,93)

    .rating
      padding-top 18px
      .title
        margin-left 18px
        font-size 14px
        line-height: 14px
        color: rgb(7,17,27)
      .rating-wrapper
        padding 0 18px
        .rating-item
          position relative
          padding 16px 0
          border-1px(rgba(7,17,27,.1))
          .user
            position: absolute
            right: 0
            top: 16px
            font-style: 0
            line-height: 12px
            .name
              vertical-align: top
              font-style: 10px
              color rgb(147,153,159)
              margin-right 6px
            .avatar
              border-radius 50%
          .time
            font-style: 10px
            line-height: 12px
            color: rgb(147,153,159)
          .rating-content
            margin-top 6px
            font-style: 0
            line-height: 16px
            .icon-thumb_up, .icon-thumb_down
              margin-right 4px
              font-style: 12px
              line-height: 16px
            .icon-thumb_up
              color: rgb(0,160,220)
            .icon-thumb_down
              color: rgb(147, 153,159)
            .text
              font-style: 12px
              color: rgb(7,17,27)


        .no-rating
          padding 16px 0
          font-size 12px
          color: rgb(147,153,159)
</style>
