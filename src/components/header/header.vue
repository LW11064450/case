<template>
  <div class="header">
    <div class="header-info clearfix">
      <div class="header-info-logo">
        <img :src="seller.avatar" alt="" width="64px" height="64px">
      </div>
      <div class="content">
          <h3 class="title">
            <i class="icon brand"></i>
            <span class="name">{{seller.name}}</span>
          </h3>
          <div class="description">
            {{seller.description}}
            /
            {{seller.deliveryTime}}分钟送达
          </div>
          <div v-if="seller.supports" class="supports">
              <i class="icon" :class="classMap[seller.supports[0].type]"></i>
              <span class="text">{{seller.supports[0].description}}</span>
          </div>
      </div>
      <div v-if="seller.supports" class="support-count">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="header-bulletin" @click="showDetail">
      <i class="icon bulletin"></i><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="bg">
      <img :src="seller.avatar" alt="" width="100%" height="100%">
    </div>
    <div class="detail" v-show="detailshow">

    </div>
  </div>
</template>

<script type="text/ecmascript-6">

  export default {
    props: {
      seller: {
        type:Object
      }
    },
    created(){
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    data(){
      return {
        detailshow: false
      }
    },
    methods: {
        showDetail() {
          this.detailshow = true;
        },
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus" type="text/stylus">
  @import "../../common/stylus/minxin.styl"
  .header
    position: relative
    color: #fff
    background: rgba(7, 17, 27, 0.5)
    overflow: hidden
    .header-info
      position: relative
      padding: 24px 12px 18px 24px
      .header-info-logo
        float: left
        width: 64px
        height: 64px
        margin-right: 16px
        img
          border-radius: 4px
      .content
        float: left
        .title
          font-size: 0
          font-weight: bold
          line-height: 18px
          margin: 2px 0 8px 0
          .brand
            width: 30px
            height: 18px
            margin-right: 6px
            bg-image('brand')
            -webkit-background-size: 30px 18px
            background-size: 30px 18px
          .name
            font-size: 16px;
            vertical-align: middle
        .description
          padding-bottom: 10px
          font-weight: 200
          line-height: 12px
        .supports
          padding-bottom: 2px
          .decrease
            width: 12px
            height: 12px
            margin-right: 4px
            -webkit-background-size: 12px
            background-size: 12px
          .text
            line-height: 12px
            font-size: 10px
            font-weight: 200
            vertical-align: middle
      .support-count
        position: absolute
        height: 24px
        line-height: 24px
        font-size: 0
        text-align: center
        padding: 0 8px
        bd-radius(14px)
        background-color: rgba(0,0,0, .2);
        right: 12px
        bottom: 18px
        .count
          font-size: 10px
          margin-right: 2px
          vertical-align: top
        .icon-keyboard_arrow_right
          line-height: 24px
          font-size: 10px
    .header-bulletin
      position: relative
      height: 28px
      line-height: 28px
      font-size: 10px
      font-weight: 200
      background-color: rgba(7,17,27, .2);
      white-space nowrap
      overflow: hidden
      text-overflow: ellipsis
      padding: 0 22px 0 12px
      .bulletin
        width: 22px
        height: 12px
        -webkit-background-size: 22px 12px
        background-size: 22px 12px
        vertical-align: top
        margin-top: 9px
      .bulletin-text
        margin: 0 4px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        top: 9px
        right: 12px

    .bg
      position: absolute
      width: 100%
      height: 100%
      top: 0
      left: 0
      z-index: -1
      filter blur(10px)

    .detail
      position: fixed
      width: 100%
      height: 100%
      background-color: rgba(7,17,27, .8);
      overflow: auto
      top: 0
      left: 0
      z-index: 100
</style>
