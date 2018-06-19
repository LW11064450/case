<template>
  <div class="shopcart">
    <div class="content">
      <div class="content-left">
        <div class="logo-wrap">
          <div class="logo" :class="{'heightlight':totalCount>0}">
            <i class="icon-shopping_cart" :class="{'heightlight':totalCount>0}"></i>
          </div>
          <div class="num" v-show="totalCount>0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'heightlight':totalCount>0}">¥{{totalPrice}}</div>
        <div class="desc">另需配送费¥{{deliveryPrice}}元</div>
      </div>
      <div class="content-right">
        <div class="pay" :class="payClass">{{payDesc}}</div>
      </div>
    </div>
    <div class="ball-container" v-for="ball in balls">
      <transition name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter">
        <div class="ball" v-show="ball.show">
          <div class="inner inner-hook"></div>
        </div>
      </transition>
    </div>
    <div class="shopcart-list">
      <div class="list-header">
        <h3 class="title">购物车</h3>
        <span class="empty">清空</span>
      </div>
      <div class="list-container">
        <ul>
          <li class="food" v-for="(item,index) in selectFoods" :key="index">
            <span class="name">{{item.name}}</span>
            <div class="price">
              <span>￥{{item.price*item.count}}</span>
            </div>
            <div class="cartcontrol-wrap">
              <cartcontrol></cartcontrol>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from '../../components/cartcontrol/cartcontrol'
  export default {
    props: {
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      },
      selectFoods: {
        type: Array,
        default() {
          return []
        }
      },
    },
    data(){
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
        dropBalls: []
      }
    },
    computed: {
      totalPrice() {
        let total = 0
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        })
        return total
      },
      totalCount:function () {
        let count = 0
        this.selectFoods.forEach((food) => {
          count += food.count
        })
        return count
      },
      payDesc:function () {
        if (this.totalPrice === 0) {
          return `¥${this.minPrice}元起送`
        }else if (this.totalPrice<this.minPrice){
          let diff = this.minPrice - this.totalPrice
          return `还差¥${diff}元配送`
        }else {
          return `去结算`
        }
      },
      payClass:function () {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough'
        }else {
          return 'enough'
        }
      }

    },
    methods:{
      drop(el) {
        //触发一次事件就会将所有小球进行遍历
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) { //将false的小球放到dropBalls
            ball.show = true;
            ball.el = el; //设置小球的el属性为一个dom对象
            this.dropBalls.push(ball);
            return;
          }
        }
      },

      beforeEnter(el){ //这个方法的执行是因为这是一个vue的监听事件
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect(); //获取小球的相对于视口的位移(小球高度)
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22); //负数,因为是从左上角往下的的方向
            el.style.display = ''; //清空display
            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
            el.style.transform = `translate3d(0,${y}px,0)`;
            //处理内层动画
            let inner = el.getElementsByClassName('inner-hook')[0]; //使用inner-hook类来单纯被js操作
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            inner.style.transform = `translate3d(${x}px,0,0)`;
          }
        }
      },

      enter(el, done) { //这个方法的执行是因为这是一个vue的监听事件
        /* eslint-disable no-unused-vars */
        let rf = el.offsetHeight; //触发重绘html
        this.$nextTick(() => { //让动画效果异步执行,提高性能
          el.style.webkitTransform = 'translate3d(0,0,0)';
          el.style.transform = 'translate3d(0,0,0)';
          //处理内层动画
          let inner = el.getElementsByClassName('inner-hook')[0]; //使用inner-hook类来单纯被js操作
          inner.style.webkitTransform = 'translate3d(0,0,0)';
          inner.style.transform = 'translate3d(0,0,0)';
          el.addEventListener('transitionend', done); //Vue为了知道过渡的完成，必须设置相应的事件监听器。
        });
      },

      afterEnter(el) { //这个方法的执行是因为这是一个vue的监听事件
        let ball = this.dropBalls.shift(); //完成一次动画就删除一个dropBalls的小球
        if (ball) {
          ball.show = false;
          el.style.display = 'none'; //隐藏小球
        }
      }
    },
    components: {
      cartcontrol
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus">
  .shopcart
    position: fixed
    width: 100%
    height: 48px
    bottom: 0
    left: 0
    z-index: 50
    .content
      display: flex
      background-color: #141d27
      font-size: 0
      color: rgba(255,255,255, .4)
      .content-left
        flex: 1
        .logo-wrap
          position: relative
          display: inline-block
          width: 56px
          height: 56px
          margin: 0 12px
          padding: 6px
          box-sizing border-box
          vertical-align: top
          border-radius: 50%
          background-color: #141d27
          top: -10px
          .logo
            width: 100%
            height: 100%
            text-align: center
            border-radius: 50%
            background-color: #2b343c
            &.heightlight
              background-color: rgb(0,160,220)
            .icon-shopping_cart
              line-height: 44px
              font-size: 24px
              color: #80858a
              &.heightlight
                color: #fff
          .num
            position: absolute
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            color: #fff
            font-size: 9px
            font-weight: 700
            background-color: rgb(240,20,20)
            border-radius: 16px
            box-shadow: 0 4px 8px 0 rgba(0,0,0, .4)
            top: 0
            right: 0
        .price
          display: inline-block
          line-height: 24px
          font-size: 16px
          font-weight: 700
          margin-top: 12px
          padding-right: 12px
          box-sizing: border-box
          border-right: 1px solid rgba(255,255,255, .1)
          &.heightlight
            color: #fff
        .desc
          display: inline-block
          line-height: 24px
          font-size: 10px
          margin: 12px 0 0 12px
      .content-right
        flex: 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          font-weight: 700
          background-color: #2b333b
          &.not-enough
            background-color: #2b333b
          &.enough
            background-color: #00b43c
            color: #fff
    .ball-container
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
        .inner
          width: 16px
          height: 16px
          border-radius: 50%
          background: rgb(0, 160, 220)
          transition: all 0.4s linear

</style>
