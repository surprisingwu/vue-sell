<template>
  <div class="seller" ref="seller">
    <div class="content">
      <div class="overview">
        <h1 class='title'>{{seller.name}}</h1>
        <div class="star-wrapper border-1px">
          <star :size='36' :score='seller.score' class='star'></star>
          <span class="rank">({{seller.ratingCount}})</span>
          <span class='sellcount'>月售{{seller.sellCount}}单</span>
        </div>
        <ul class="delivery">
          <li class="delivery-item">
            <h1 class="title">起送价</h1>
            <div class="content">
              <span class="text">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="delivery-item">
            <h1 class="title">商家配送</h1>
            <div class="content">
              <span class="text">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="delivery-item">
            <h1 class="title">平均配送时间</h1>
            <div class="content">
              <span class="text">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleColor($event)">
          <i class="icon-favorite" :class="{active:toggle}"></i>
          <div class='text'>{{toggle ? "收藏" : "未收藏"}}</div>
        </div>
      </div>
      <split></split>
      <div class="notice-wrapper">
        <h1 class="title">公告与活动</h1>
        <p class="detail border-1px">{{seller.bulletin}}</p>
        <ul>
          <li class='support border-1px' v-show="seller.supports&&seller.supports.length"
              v-for="(support,index) in seller.supports" :key="index">
            <span class="icon" :class="classMap[support.type]"></span><span class="text">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="sell-liveaction">
        <h1 class="title">商家实景</h1>
        <div class="photocontainer" ref='pics'>
          <ul class="img-wrapper" ref='piclist'>
            <li class="item" v-for="(pic,I) in seller.pics" :key="I">
              <img :src="pic" width="120px" height="90px" class="img">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="sellermesg-wrapper">
        <h1 class='title'>商家信息</h1>
        <ul>
          <li class="mesg-item" v-for="(item,J) in seller.infos" :key="J">
            <span class="text">{{item}}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script type="text/ecmascript-6">
  import {saveToLocal, loadFromLocal} from 'common/js/stor'
  import star from 'components/star/star'
  import BSscroll from 'better-scroll'
  import split from 'components/split/split'

  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        toggle: (() => {
          return loadFromLocal(this.seller.id, 'toggle', false)
        })()
      }
    },
    components: {
      star, split
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    watch: {
      'seller'() {
        this.$nextTick(() => {
          this._initscroll()
          this._initpics()
        })
      }
    },
    mounted() {
      this.$nextTick(() => {
        this._initscroll()
        this._initpics()
      })
    },
    methods: {
      toggleColor: function (event) {
        if (!event._constructed) {
          return
        }
        this.toggle = !this.toggle
        saveToLocal(this.seller.id, 'toggle', this.toggle)
      },
      _initscroll() {
        if (!this.scroll) {
          this.scroll = new BSscroll(this.$refs.seller, {
            click: true
          })
        } else {
          this.scroll.refresh()
        }
      },
      _initpics() {
        if (this.seller.pics) {
          let picWidth = 120
          let margin = 6
          let width = (picWidth + margin) * this.seller.pics.length - margin
          this.$refs.piclist.style.width = width + 'px'
        }
        if (!this.picsScroll) {
          this.picsScroll = new BSscroll(this.$refs.pics, {
            scrollX: true,
            eventPassthrough: 'vertical'
          })
        } else {
          this.picsScroll.refresh()
        }
      }
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .seller
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .overview
      position relative
      margin 18px
      .title
        margin-bottom 8px
        line-height: 14px
        font-size 14px
        font-weight 700
        color: rgb(7, 17, 27)
      .star-wrapper
        position relative
        padding-bottom 18px
        border-1px(rgba(7, 17, 27, .1))
        font-size 0
        .star, .rank, .sellcount
          display inline-block
          vertical-align top
          line-height: 18px
        .rank, .sellcount
          font-size 10px
          color: rgb(77, 85, 93)
        .rank
          margin-left 8px
        .sellcount
          margin-left 12px

      .delivery
        display flex
        padding-top 18px
        .delivery-item
          flex 1
          text-align center
          border-right 1px solid rgba(7, 17, 27, .1)
        .delivery-item:last-child
          border-right none
        .title
          margin-bottom 4px
          line-height: 10px
          font-size 10px
          color rgb(147, 153, 159)
        .content
          line-height: 24px
          font-size 10px
          color: rgb(7, 17, 27)
        .text
          font-size 24px

      .favorite
        position absolute
        top: 0
        right: 0
        width 50px
        text-align center
        .icon-favorite
          margin-bottom 4px
          line-height: 24px
          font-size 24px
          color: #d4d6d9
          &.active
            color: rgb(240, 20, 20)
        .text
          line-height: 10px
          font-size 10px
          color: rgb(77, 85, 93)
    .notice-wrapper
      padding 0 18px
      .title
        margin 18px 0 8px 0
        line-height: 14px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .detail
        padding 0 12px 16px
        line-height: 24px
        font-size 12px
        border-1px(rgba(7, 17, 27, .1))
        color: rgb(240, 20, 20)
      .support
        padding 16px 12px
        font-size 0
        line-height: 16px
        border-1px(rgba(7, 17, 27, .1))
        &:last-child
          border-none()
        .icon
          display inline-block
          margin-right 6px
          vertical-align: top
          width 16px
          height 16px
          background-size 16px 16px
          background-repeat no-repeat
          &.decrease
            bg-img("decrease_4")
          &.discount
            bg-img("discount_4")
          &.guarantee
            bg-img("guarantee_4")
          &.special
            bg-img("special_4")
          &.invoice
            bg-img("invoice_4")
        .text
          display inline-block
          vertical-align top
          font-size 12px
    .sell-liveaction
      padding 18px 0 18px 18px
      .title
        margin-bottom 12px
        line-height: 14px
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
      .photocontainer
        width 100%
        overflow hidden
        white-space nowrap
        .img-wrapper
          width 100%
          font-size 0
          .item
            display inline-block
            width 120px
            margin-right 6px
            height 90px
            &:last-child
              margin-right 0

    .sellermesg-wrapper
      padding 0 18px
      .title
        padding-top 18px
        padding-bottom  12px
        line-height: 14px
        font-size 14px
        font-weight 700
        border-1px(rgba(7, 17, 27, .1))
        color rgb(7, 17, 27)
      .mesg-item
        padding 16px 12px
        line-height: 16px
        border-1px(rgba(7, 17, 27, .1))
        &:last-child
          border-none()
        .text
          font-size 12px
          color: rgb(7, 17, 27)
</style>
