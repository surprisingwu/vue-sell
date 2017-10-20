<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img v-bind:src='seller.avatar' width="64" height="64">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="supports">
          <span class="icon" :class='classMap[seller.supports[0].type]'></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if='seller.supports' class="support-count" @click="showDetailHandler">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetailHandler">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img v-bind:src="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="detail-title">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class='support-item' v-for="(item,index) in seller.supports" :key="index">
                <span class='icon' :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{item.description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="text">
                {{seller.bulletin}}
              </p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="closeDetailShow">
          <i class="icon-close"></i>
        </div>
      </div>
    </transition>

  </div>
</template>
<script type="text/ecmascript-6">
  import Star from 'components/star/star'
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        detailShow: false
      }
    },
    components: {
      Star
    },
    methods: {
      showDetailHandler() {
        this.detailShow = true
      },
      closeDetailShow() {
        this.detailShow = false
      }
    },
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .header
    position: relative
    overflow: hidden
    color: #fff
    background-color: rgba(7, 17, 27, .5)
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      font-size: 0
      .avatar
        display: inline-block
        vertical-align: top
        & > img
          border-radius: 2px
      .content
        display: inline-block
        margin-left: 16px
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            margin-right: 6px
            width: 30px
            height: 18px
            bg-img('brand')
            background-size: 30px 18px
            background-repeat: no-repeat
          .name
            vertical-align: top
            font-size: 16px
            font-weight: bold
            line-height: 18px
        .description
          font-size: 12px
          line-height: 12px
        .supports
          margin 10px 0 2px
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-img("decrease_1")
            &.discount
              bg-img("discount_1")
            &.guarantee
              bg-img("guarantee_1")
            &.special
              bg-img("special_1")
            &.invoice
              bg-img("invoice_1")
          .text
            line-height: 12px
            margin-left: 4px
            font-size: 10px
      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        background-color: rgba(0, 0, 0, .2)
        border-radius: 12px
        .count
          font-size: 10px
          line-height: 24px
          vertical-align: top
        .icon-keyboard_arrow_right
          margin-left: 2px
          font-size: 10px
          line-height: 24px
    .bulletin-wrapper
      position: relative
      padding 0 22px 0 12px
      height: 28px
      line-height: 28px
      overflow: hidden
      white-space: nowrap
      text-overflow: ellipsis
      background-color: rgba(7, 17, 27, .2)
      .bulletin-title
        display: inline-block
        vertical-align: top
        margin-top: 8px
        width: 22px;
        height: 12px
        bg-img("bulletin")
        background-size: 22px 12px
        background-repeat: no-repeat
      .bulletin-text
        vertical-align: top
        margin: 0 4px
        font-size: 10px
      .icon-keyboard_arrow_right
        position: absolute
        right: 12px
        top: 8px
        font-size: 10px
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
    .detail
      position: fixed
      z-index: 100
      top: 0
      left: 0
      width: 100%
      height: 100%
      overflow: auto
      opacity: 1
      background-color: rgba(7, 17, 27, 0.8)
      backdrop-filter: blur(10px)
      &.fade-enter-active,&.fade-leave-active
        transition: all .5s
      &.fade-enter,&.fade-leave-to
        opacity: 0
        background-color: rgba(7, 17, 27, 0)
      .detail-wrapper
        min-height: 100%
        width: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .detail-title
            text-align: center
            font-size: 16px
            font-weight: 700
            line-height: 16px
          .star-wrapper
            margin-top: 16px
            padding: 2px 0
            text-align: center
          .title
            display: flex
            width: 80%
            margin 28px auto 24px auto
            .line
              position: relative
              top: -6px
              flex: 1
              border-bottom 1px solid rgba(255, 255, 255, .2)
            .text
              padding: 0 12px
              font-size: 14px
              font-weight: 700
              line-height: 14px
          .supports
            width: 80%
            margin: 0 auto
            .support-item
              padding: 0 12px
              margin-bottom: 12px
              font-size: 0
              &:last-child
                margin-bottom:  0
              .icon
                display inline-block
                vertical-align: top
                width: 16px
                height: 16px
                margin-right: 6px
                background-size: 16px 16px
                background-repeat: no-repeat
                &.decrease
                  bg-img("decrease_2")
                &.discount
                  bg-img("discount_2")
                &.guarantee
                  bg-img("guarantee_2")
                &.special
                  bg-img("special_2")
                &.invoice
                  bg-img("invoice_2")
              .text
                font-size: 12px
                line-height: 16px
          .bulletin
            width: 80%
            margin: 0 auto
            .text
              padding: 0 12px
              font-size: 12px
              line-height: 24px
      .detail-close
        position: relative;
        width: 32px;
        height: 32px;
        margin: -64px auto 0;
        clear: both;
        font-size: 32px;

</style>
