<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :key="index" :class="{'current': index == _currentIndex}"
            @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <span class="icon" v-show="item.type>-1" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li class="foods-list food-item-hook" v-for="(item,index) in goods" :key="index">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li class="food-item" v-for="(food,_index) in item.foods" :key="_index" @click="selectFood(food,$event)">
              <div class="icon">
                <img :src=" food.icon" width="57px" height="57px">
              </div>
              <div class="content">
                <h2 class="content-title">{{food.name}}</h2>
                <div class="desc" v-show="food.description">{{food.description}}</div>
                <div class="some-mesg">
                  <span class="sell-count">月售{{food.sellCount}}份</span><span class="rating"
                                                                             v-show="food.rating>0">{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now-price">￥{{food.price}}</span>
                  <span v-show="food.oldPrice>0" class="old-price">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartControl :food="food" @add='addFood'></cartControl>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcut  ref='shopcart' :select-foods='selectFoods' :delivery-price='seller.deliveryPrice' :min-price="seller.minPrice"></shopcut>
    <Food ref='showFoodDetail' @add='addFood' :food = 'selectedFood'></Food>
  </div>
</template>
<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopcut from 'components/shopcut/shopcut'
  import cartControl from 'components/cartcontrol/cartcontrol'
  import Food from 'components/food/food'

  const ERR_OK = 0
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    computed: {
      _currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i + 1]
          if (!height2 || (this.scrollY < height2 && this.scrollY >= height1)) {
            return i
          }
        }
      },
      selectFoods() {
        let foods = []
        for (let key in this.goods){
          this.goods[key].foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        }
        return foods
      }
    },
    created() {
      this.$http.get('/api/goods').then(function (response) {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = Object.assign({}, this.goods, response.data)
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    },
    methods: {
      _initScroll() {
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        })
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        })
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      addFood (target) {
        this._drop(target)
      },
      _drop(target) {
        // 体验优化，异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target)
        })
      },
      _calculateHeight() {
        let foodlist = this.$refs.foodsWrapper.getElementsByClassName('food-item-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i < foodlist.length; i++) {
          let foodItem = foodlist[i]
          height += foodItem.clientHeight
          this.listHeight.push(height)
        }
      },
      selectMenu(index, event) {
        if (!event._constructed) {
          return
        }
        let targetPosition = this.$refs.foodsWrapper.getElementsByClassName('food-item-hook')[index]
        this.foodsScroll.scrollToElement(targetPosition, 300)
      },
      selectFood(food, event) {
        if (!event._constructed) {
          return
        }
        this.selectedFood = food
        this.$refs.showFoodDetail.show()
      }
    },
    components: {
      shopcut, cartControl, Food
    }
  }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background-color #f3f5f7
      .menu-item
        display: table
        padding: 0 12px
        width: 56px
        height: 54px
        line-height: 14px
      .current
        position: relative
        z-index: 10
        margin-top: -1px
        background-color: #fff
        .text
          font-weight: 700
          border-none()
      .text
        display: table-cell
        vertical-align: middle
        border-1px(rgba(7, 17, 27, .1))
        width 56px
        font-size: 12px
        .icon
          display inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-img("decrease_3")
          &.discount
            bg-img("discount_3")
          &.guarantee
            bg-img("guarantee_3")
          &.special
            bg-img("special_3")
          &.invoice
            bg-img("invoice_3")

    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px
        border-left: 2px solid #d9dde1
        height: 26px
        line-height: 26px
        font-size: 12px
        color rgb(147, 153, 159)
        background-color #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, .1))
        &:last-child
          margin-bottom: 0
          border-none()
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          font-size: 0
          .content-title
            margin: 2px 0 8px 0
            font-size: 14px
            line-height: 14px
            color: rgb(7, 17, 27)
          .desc
            margin-bottom: 8px
            font-size: 10px
            line-height: 14px
            color: rgb(147, 153, 159)
          .some-mesg
            font-siz: 0
            .sell-count, .rating
              font-size: 10px
              line-height: 10px
              color: rgb(147, 153, 159)
            .sell-count
              margin-right: 12px
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
            right: 0
            bottom: 16px
</style>
