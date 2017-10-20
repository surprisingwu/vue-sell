<template>
  <div class="shopcut">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight': totalCount>0}">
            <i class="icon-shopping_cart" :class="{'highlight': totalCount>0}"></i>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalMoney>0}">￥{{totalMoney}}</div>
        <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right" :class="payClass" @click.stop.prevent='pay'>{{payDesc}}</div>
    </div>
    <div class="balls-container">
      <transition-group name="dropAnimation" tag='div' @before-enter="beforeEnterDrop" @enter="enterDrop"
                        @after-enter="afterEnterDrop">
        <div class="ball" v-for="(ball,index) in balls" v-show="ball.show" :key='index'>
          <div class="inner inner-hook"></div>
        </div>
      </transition-group>
    </div>
    <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref='foldlist'>
          <ul>
            <li class="food" v-for="(food,index) in selectFoods" :key="index">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>￥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol @add = 'drop' :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="fade" >
      <div class="list-mask" @click="hideMask" v-show='listShow'></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol'
  import BScroll from 'better-scroll'

  export default {
    props: {
      minPrice: {
        type: Number,
        default: 0
      },
      deliveryPrice: {
        type: Number,
        default: 0
      },
      selectFoods: {
        type: Array,
        default() {
          return []
        }
      }
    },
    data() {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropballs: [],
        fold: true
      }
    },
    computed: {
      totalMoney() {
        let total = 0
        this.selectFoods.forEach((food) => {
          total += food.price * food.count
        })
        return total
      },
      totalCount() {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      },
      payDesc() {
        if (this.totalMoney === 0) {
          return `￥${this.minPrice}起送`
        } else if (this.totalMoney < this.minPrice) {
          let diff = this.minPrice - this.totalMoney
          return `还差￥${diff}起送`
        } else {
          return `去结算`
        }
      },
      payClass() {
        if (this.totalMoney < this.minPrice) {
          return 'not-enough'
        } else {
          return 'enough'
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true
          return
        }
        let show = !this.fold
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs.foldlist, {
                click: true
              })
            } else {
              this.scroll.refresh()
            }
          })
        }
        return show
      }
    },
      methods: {
        drop(el) {
          for (let i = 0; i < this.balls.length; i++) {
            let ball = this.balls[i]
            if (!ball.show) {
              ball.show = true
              ball.el = el
              this.dropballs.push(ball)
              return
            }
          }
        },
        add(target) {
          this.drop(target)
        },
        beforeEnterDrop(el) {
          let count = this.balls.length
          while (count--) {
            let ball = this.balls[count]
            if (ball.show) {
              let rect = ball.el.getBoundingClientRect()
              let x = rect.left - 32
              let y = -(window.innerHeight - rect.top - 22)
              el.style.display = ''
              el.style.webkitTransform = `translate3d(0,${y}px,0)`
              el.style.transform = `translate3d(0,${y}px,0)`
              let inner = el.getElementsByClassName('inner-hook')[0]
              inner.style.webkitTransform = `translate3d(${x}px,0,0)`
              inner.style.transform = `translate3d(${x}px,0,0)`
            }
          }
        },
        enterDrop(el, done) {
          /* eslint-disable no-unused-vars */
          let rf = el.offsetHeight
          this.$nextTick(() => {
            el.style.webkitTransform = 'translate3d(0,0,0)'
            el.style.transform = 'translate3d(0,0,0)'
            let inner = el.getElementsByClassName('inner-hook')[0]
            inner.style.webkitTransform = 'translate3d(0,0,0)'
            inner.style.transform = 'translate3d(0,0,0)'
            el.addEventListener('transitionend', done)
          })
        },
        afterEnterDrop(el) {
          let ball = this.dropballs.shift()
          if (ball) {
            ball.show = false
            el.style.display = 'none'
          }
        },
        toggleList() {
          if (!this.totalCount) {
            return
          }
          this.fold = !this.fold
        },
        empty() {
          this.selectFoods.forEach((food) => {
            food.count = 0
          })
        },
        hideMask() {
          this.fold = true
        },
        pay() {
          if (this.totalMoney < this.minPrice) {
            return
          }
          window.alert(`需支付${this.totalMoney}元`)
        }
      },
      components: {
        cartcontrol
      }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .shopcut
    position: fixed
    left: 0
    bottom: 0
    width: 100%
    height: 48px
    z-index: 99
    .content
      display: flex
      font-size: 0
      color: rgba(255, 255, 255, 0.4)
      background-color: #141d27
      .content-left
        flex: 1
        .logo-wrapper
          display: inline-block
          position: relative
          top: -10px
          vertical-align: top
          margin: 0 12px
          padding: 6px
          height: 56px
          width: 56px
          box-sizing: border-box
          border-radius: 50%
          background-color: #141d27
          .logo
            text-align: center
            height: 100%
            width: 100%
            border-radius: 50%
            background-color: #2b343c
            &.highlight
              background-color: rgb(0, 160, 220)
            .icon-shopping_cart
              font-size: 24px
              line-height: 44px
              color: #80858a
              &.highlight
                color: #fff
          .num
            position absolute
            right: 0
            top: 0
            width 24px
            height 16px
            line-height: 16px
            border-radius: 16px
            font-size: 9px
            font-weight: 700
            text-align center
            box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4)
            background-color: rgb(240, 20, 20)
            color: #fff
        .price
          display: inline-block
          vertical-align: top
          margin-top: 12px
          padding-right: 12px
          font-size: 16px
          font-weight: 700
          line-height: 24px
          border-right: 1px solid rgba(255, 255, 255, .1)
          &.highlight
            color: #fff
        .desc
          display: inline-block
          padding: 0 12px
          font-size: 10px
          line-height: 48px
          color: rgba(255, 255, 255, .4)
      .content-right
        flex: 0 0 105px
        padding: 0 8px
        text-align: center
        height: 48px
        line-height: 48px
        font-size: 12px
        font-weight: 700
        background-color rgb(43, 51, 59)
        &.not-enugh
          background-color: rgb(43, 51, 59)
        &.enough
          background-color: #00b43c
          color: #fff

    .balls-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background-color: rgb(0, 160, 220)
        &.dropAnimation-enter-active
          transition: all .3s cubic-bezier(0.49, -0.29, 0.75, 0.41)
          .inner
            transition: all .3s linear
    .shopcart-list
      position: absolute
      left: 0
      top: 0
      z-index: -1
      width: 100%
      transform: translate3d(0, -100%, 0)
      &.fold-enter-active, &.fold-leave-active
        transition: all .5s
      &.fold-enter, &.fold-leave-to
        transform: translate3d(0, 0, 0)
      .list-header
        height: 40px
        line-height: 40px
        padding: 0 18px
        background: #f3f5f7
        border-bottom: 1px solid rgba(7, 17, 27, 0.1)
        .title
          float: left
          font-size: 14px
          color: rgb(7, 17, 27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0, 160, 220)
      .list-content
        padding: 0 18px
        max-height: 217px
        overflow: hidden
        background-color: #fff
        .food
          padding 12px 0
          position relative
          width 100%
          box-sizing border-box
          border-1px(rgba(7, 17, 27, .1))
          .name
            line-height: 24px
            font-size 14px
            color rgb(7, 17, 27)
          .price
            position absolute
            right 90px
            bottom: 12px
            line-height: 24px
            font-size: 14px
            font-weight: 700
            color: rgb(240, 20, 20)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 6px

    .list-mask
      position: fixed;
      top: 0
      left: 0
      height: 100%
      width: 100%
      z-index: -2
      overflow hidden
      background-color rgba(7,17,27,.6)
      -webkit-backdrop-filter blur(10px)
      backdrop-filter blur(10px)
      &.fade-enter-active, &.fade-leave-active
        transition all .5s
      &.fade-enter, &.fade-leave-to
        opacity 0
        background-color rgba(7,17,27,0)
      &.fade-enter-to, &.fade-leave
        opacity: 1
        background-color rgba(7,17,27,.6)
</style>
