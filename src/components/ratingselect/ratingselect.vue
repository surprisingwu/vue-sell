<template>
  <div class="ratingselect">
    <div class="ratingtype border-1px">
      <span class="block positive" @click="selectType(2,$event)" :class="{'active':sType === 2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span class="block positive" @click="selectType(0,$event)" :class="{'active':sType === 0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span class="block negative" @click="selectType(1,$event)" :class="{'active':sType === 1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div class="switch" @click='isOn' :class="{'on':oContent}">
      <i class="icon-check_circle"></i>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE = 0
  const NEGATIVE = 1
  const ALL = 2
    export default {
      props: {
        ratings: {
          type: Array,
          default() {
            return []
          }
        },
        selecttype: {
          type: Number,
          default: ALL
        },
        onlyContent: {
          type: Boolean,
          default: false
        },
        desc: {
          type: Object,
          default() {
            return {
              all: '全部',
              positive: '满意',
              negative: '不满意'
            }
          }
        }
      },
      computed: {
        positives() {
          return this.ratings.filter((rating) => {
            return rating.rateType === POSITIVE
          })
        },
        negatives() {
          return this.ratings.filter((rating) => {
            return rating.rateType === NEGATIVE
          })
        }
      },
      data() {
        return {
          sType: this.selecttype,
          oContent: this.onlyContent
        }
      },
      methods: {
        selectType(type, event) {
          if (!event._constructed) {
            return
          }
          this.sType = type
          this.$emit('select', type)
        },
        isOn(event) {
          if (!event._constructed) {
            return
          }
          this.oContent = !this.onlyContent
          this.$emit('isonlycontent')
        }
      }
    }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratingselect
    .ratingtype
      padding 18px 0
      margin 0 18px
      border-1px(rgba(7,17,27,.1))
      font-size 12px
      .block
        display inline-block
        padding 8px 12px
        margin-right 8px
        line-height 16px
        font-size 12px
        border-radius 1px
        color: rgb(77,85,93)
        &.active
          color: #fff
        .count
          margin-left: 2px
          font-size: 8px
        &.positive
          background-color rgba(0, 160, 220, .2)
          &.active
           background-color rgb(0, 160, 220)
        &.negative
          background-color rgba(77, 85, 93, .2)
          &.active
            background-color rgb(77, 85, 93)

    .switch
      padding 12px 18px
      line-height 24px
      border-bottom 1px solid rgba(7, 17, 27, .1)
      color rgb(147, 153, 159)
      font-size 0
      &.on
        .icon-check_circle
          color: #00c850
      .icon-check_circle
        display inline-block
        vertical-align top
        margin-right 4px
        font-size 24px
      .text
        display inline-block
        vertical-align top
        font-size 12px
</style>
