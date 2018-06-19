<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click="decreaseCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue'
  export default {
    props:{
      food: {
        type: Object
      }
    },
    created(){
    },
    methods: {
      addCart:function (event) {
        if (!event._constructed) {
          return
        }
        if (!this.food.count) {
          Vue.set(this.food,'count',1)
        }else {
          this.food.count++
        }
        this.$emit('cart-add',event.target)
      },
      decreaseCart:function (event) {
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

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      transition: all 0.4s linear
      .inner
        display: inline-block
        font-size: 24px
        line-height: 24px
        color: rgb(0,160,220)
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3d(24px, 0, 0)
    .cart-count
      display: inline-block
      font-size: 10px
      color: rgb(147,153,159)
      line-height: 24px
      padding-top: 6px
      vertical-align: top
    .cart-add
      display: inline-block
      font-size: 24px
      line-height: 24px
      color: rgb(0,160,220)
      padding: 6px


</style>
