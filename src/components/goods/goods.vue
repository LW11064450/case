<template>
  <div class="goods">
    <div class="menu-main" ref="menuMain">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}"
        @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <i v-show="item.type>0" class="icon" :class="classMap[item.type]"></i>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="goods-main" ref="goodsMain">
      <ul>
        <li v-for="item in goods" class="goods-list goods-list-hook">
          <h3 class="title">{{item.name}}</h3>
          <ul>
            <li v-for="food in item.foods" class="goods-item">
              <div class="goods-img">
                <img :src="food.icon" alt="" width="57px" height="57px">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span v-show="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrap">
                  <cartcontrol @cart-add="addFood" :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart ref="shopcart" :select-foods="selectFoods" :deliveryPrice="seller.deliveryPrice" :minPrice="seller.minPrice"></shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll'
  import shopcart from '../../components/shopcart/shopcart'
  import cartcontrol from '../../components/cartcontrol/cartcontrol'

  const ERR_OK = 0

  export default {
    props: {
      seller:{
        type: Object
      }
    },
    data(){
      return {
        goods: [],
        listHeight: [],
        scrollY: 0
      }
    },
    computed:{
      currentIndex: function () {
        for (var i = 0; i<this.listHeight.length; i++) {
          let height1 = this.listHeight[i]
          let height2 = this.listHeight[i+1]
          if (!height2 || (this.scrollY>=height1 && this.scrollY < height2)) {
            return i
          }
        }
        return 0
      },
      selectFoods:function () {
        let foods = []
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food)
            }
          })
        })
        return foods
      }
    },
    created(){
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee','bulletin'];
      this.$http.get("./api/goods").then((response) => {
        response = response.body
        if (response.errno === ERR_OK) {
          this.goods = response.data
          this.$nextTick(() => {
            this._initScroll()
            this._calculateHeight()
          })
        }
      })
    },
    methods:{
      _initScroll: function () {
        this.menuScroll = new BScroll(this.$refs.menuMain,{
          click: true
        })
        this.goodScroll = new BScroll(this.$refs.goodsMain,{
          click: true,
          probeType: 3
        })

        this.goodScroll.on('scroll',(pos)=>{
          this.scrollY = Math.abs(Math.round(pos.y))
        })
      },
      _calculateHeight: function () {
        let goodsList = this.$refs.goodsMain.getElementsByClassName('goods-list-hook')
        let height = 0
        this.listHeight.push(height)
        for (let i = 0; i<goodsList.length; i++) {
          let item = goodsList[i]
          height += item.clientHeight
          this.listHeight.push(height)
        }
      },
      addFood:function(target){
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target)
        })
      },
      /*_drop:function(target){
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target)
        })
      },*/
      selectMenu: function (index,event) {
        if (!event._constructed) {
          return
        }
        let goodsList = this.$refs.goodsMain.getElementsByClassName('goods-list-hook')
        let el = goodsList[index]
        this.goodScroll.scrollToElement(el,300)
      },
    },
    components: {
      shopcart,
      cartcontrol
    },
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus">
  @import "../../common/stylus/minxin.styl"
  .goods
    position: absolute
    display: flex
    width: 100%
    top: 174px
    bottom: 46px
    overflow: hidden
    .menu-main
      flex: 0 0 80px
      width: 80px
      background-color: #f3f5f7
      .menu-item
        display: table
        width: 56px
        height: 54px
        line-height: 14px
        padding: 0 12px
        &.current
          font-weight: 700
          margin-top: -1px
          background-color: #fff;
          .text
            border-none()
        .text
          display: table-cell
          width: 56px
          font-size: 12px
          vertical-align: middle
          border-1px(rgba(7,17,27, .1))
          .icon
            width: 12px
            height: 12px
            margin-right: 2px
            -webkit-background-size: 12px 12px
            background-size: 12px 12px
    .goods-main
      flex: 1
      .title
        height: 26px
        line-height: 26px
        font-size: 12px
        padding-left: 14px
        color: rgb(147,153,159)
        background-color: #f3f5f7;
        border-left: 2px solid #d9dde1
      .goods-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7,17,27, .1))
        &:last-child
          border-none()
          margin-bottom: 0
        .goods-img
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7,17,27)
            margin: 2px 0 8px 0
          .desc,.extra
            font-size: 10px;
            line-height: 12px
            color: rgb(147,153,159)
          .desc
            margin-bottom: 8px
          .extra
            .count
              margin-right: 12px
          .price
            line-height: 24px
            font-weight: 700
            .now
              font-size: 14px
              margin-right: 8px
              color: rgb(240,20,20)
            .old
              text-decoration: line-through
              font-size: 10px
          .cartcontrol-wrap
            position: absolute
            right: 0
            bottom: 12px


</style>


