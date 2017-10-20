<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" @click.stop.prevent="decreaseCart" v-show="food.count>0">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop.prevent="addcart"></div>
  </div>
</template>
<script type="text/ecmascript-6">
  import Vue from 'vue'
  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addcart(event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1)
        } else {
          this.food.count++
        }
        this.$emit('add', event.target)
      },
      decreaseCart(event) {
        if (!event._constructed) {
          return
        }
        if (this.food.count) {
          this.food.count--
        }
      }
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    .cart-decrease
      display: inline-block
      padding: 6px
      .inner
        display inline-block
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 220)
        opacity: 1
      &.move-enter-active, &.move-leave-active
        transition: all  0.4s linear
        transform: translate3d(0, 0, 0)
        .inner
          transition all 0.4s linear
      &.move-enter-to, &.move-leave
          opacity: 1
          .inner
            transform rotate(0)
      &.move-enter, &.move-leave-to
        opacity: 0
        transform: translate3d(24px, 0, 0)
        .inner
          transform rotate(180deg)
    .cart-count
      display: inline-block
      vertical-align: top
      margin-top: 6px
      text-align: center
      width: 12px
      line-height: 24px
      font-size: 10px
      color: rgb(147, 153, 159)
    .cart-add
      display: inline-block
      padding: 6px
      font-size: 24px
      line-height: 24px
      color: rgb(0, 160, 220)
</style>
