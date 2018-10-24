<template>
  <div><div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" :key="index" class="menu-item" :class="{'current':currentIndex === index}"
            @click="selectMenu(index)">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="(item, index) in goods" :key="index" class="foods-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="(food, index2) in item.foods" @click="selectFood(food)" :key="index2" class="food-item border-1px">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}件</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" v-on:add="_drop"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart ref="shopcart" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"
              :selectFoods="selectFoods"></shopcart>
  </div>
  <food :food="selectedFood" ref="food" v-on:add="_drop"></food></div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopcart from '../shopcart/shopcart'
  import cartcontrol from '../cartcon/cartcontrol'
  import food from '../food/food'
  const ERR_OK = 0
  export default {
    name: 'goods',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      }
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          // console.log('height1' + height1)
          let height2 = this.listHeight[i + 1];
          // console.log('height2' + height2)
          // console.log(this.scrollY + 1)
          if (!height2 || (this.scrollY + 1 >= height1 && this.scrollY + 1 < height2)) { // 这里scrollY不知道为啥出了问题，只好+1
            // console.log('i' + i)
            return i
          }
        }
        return 0
      },
      selectFoods () {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        });
        return foods
      }
    },
    created () {
      this.$http.get('/apis/goods').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.goods = response.data;
          this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight()
          })
        }
      });
      this.classMap = ['decrease', 'discount', 'guarantee', 'invoice', 'special']
    },
    methods: {
      selectMenu (index) {
        // console.log(index)
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300)
      },
      _drop (el) {
        // this.$nextTick(() => {
          this.$refs.shopcart.drop(el)
        // })
      },
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3 // 滚动时可以实时获得位置
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y))
          // console.log(this.scrollY)
        })
      },
      _calculateHeight () {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          height += foodList[i].clientHeight;
          this.listHeight.push(height)
          // console.log('rua' + height)
        }
      },
      selectFood (food) {
        this.selectedFood = food;
        this.$refs.food.show()
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin"
  @import "../../common/stylus/base"
  .goods
    display:flex
    position: absolute
    top:174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display:table
        padding:0 12px
        height: 54px
        width: 56px
        &.current
          position: relative
          margin-top: -1px
          background: #fff
          .text
            border-none()
        .icon
          display: inline-block
          vertical-align top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell
          width:56px
          vertical-align: middle
          font-size: 12px
          line-height: 14px
          font-weight: 200
          border-1px(rgba(7, 17, 27, 0.1))
    .foods-wrapper
      flex: 1
      background: white
      .foods-list
        .title
          padding-left: 14px
          font-size:12px
          color: rgb(147,153,159)
          line-height: 26px
          height: 26px
          border-left: 2px solid #d9dde1
          background: #f3f5f7
        .food-item
          display: flex
          margin: 18px
          border-1px(rgba(7, 17, 27, 0.1))
          padding-bottom: 18px
          &:last-child
            border-none()
            margin-bottom: 0
          .icon
            margin-right:10px
          .content
            flex:1
            .name
              margin: 2px 0 8px 0
              font-size:14px
              color: rgb(7,17,27)
              line-height:14px
              height: 14px
            .desc, .extra
              font-size: 10px
              color: rgb(147,153,159)
              line-height: 10px
            .desc
              margin-bottom: 8px
              line-height: 12px
            .extra .count
              margin-right:12px
            .price
              line-height: 24px
              .now
                color: rgb(240,20,20)
                line-height: 24px
                font-size: 14px
                font-weight: 700
              .old
                vertical-align: top
                text-decoration:line-through
                color: grey
                font-size: 10px
            .cartcontrol-wrapper
              position: absolute
              right: 0
              bottom: 12px

</style>
