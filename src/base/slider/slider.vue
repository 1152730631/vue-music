<template>
  <div class="slider" ref="slider">
    <div class="slider-group" ref="sliderGroup">
      <slot>
      </slot>
    </div>
    <div class="dots">
      <span class="dot" v-for="(item,index) in dots" :class="{active:currentPageIndex === index}"></span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from  'better-scroll'
  import {addClass} from  'common/js/dom'

  export default {
    name: 'slider',
    props: {
      loop: {
        type: Boolean,
        default: true
      },
      autoPlay: {
        type: Boolean,
        default: true
      },
      interval: {
        type: Number,
        default: 4000
      }
    },
    data() {
      return {
        dots: [],
        currentPageIndex: 0
      }
    },
    mounted() {
      setTimeout(() => {
        this._setSliderWidth()
        this._initDots()
        this._initSlider()

        /*如果允许自动播放*/
        if(this.autoPlay){
          this._play();
        }
      }, 20)

      /*监听窗口尺寸改变事件 当窗口改变从新计算宽度*/
      window.addEventListener("resize",()=>{
        if(!this.slider){
          return
        }
        this._setSliderWidth(true)
        this.slider.refresh()//刷新
      })

    },
    methods:{
      /*设置轮播图宽度*/
      _setSliderWidth(isResize){
        this.children = this.$refs.sliderGroup.children
        let width = 0;
        let sliderWidth = this.$refs.slider.clientWidth
        for(let i = 0;i < this.children.length;i++){
          let child = this.children[i]
          /*为没一个子元素添加样式*/
          addClass(child,'slider-item')
          /*设置宽度*/
          child.style.width = sliderWidth + "px"
          width += sliderWidth
        }

        /*如果设置无限循环*/
        if(this.loop && !isResize){
          width += 2 * sliderWidth
        }
        this.$refs.sliderGroup.style.width = width + "px"

      },
      _initSlider() {

        this.slider = new BScroll(this.$refs.slider,{
          scrollX       : true,
          scrollY       : false,
          momentum      : false,
          snap          : true,
          snapLoop      : this.loop,
          snapThreshold : 0.3,
          snapSpeed     : 400,
        })

        /*监听轮播图滚动事件 获取当前Index减一后设置currentPageIndex用于改变指示点*/
        this.slider.on('scrollEnd',()=>{
          let pageIndex = this.slider.getCurrentPage().pageX
          if(this.loop){
            pageIndex -= 1
          }
          this.currentPageIndex = pageIndex

          /*监听轮播图滚动事件 设置自动轮播*/
          if(this.autoPlay){
            clearTimeout(this.timer);
            this._play();
          }

        })
      },
      /*初始化指示点*/
      _initDots(){
        this.dots = new Array(this.children.length)
      },
      /*设置自动播放*/
      _play(){
        let pageIndex = this.currentPageIndex + 1
        if(this.loop){
          pageIndex += 1
        }
        this.timer = setTimeout(()=>{
          this.slider.goToPage(pageIndex,0,400)
        },this.interval)
      }
    },
    /*组件销毁时清空计时器*/
    destroyed(){
      clearTimeout(this.timer)
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .slider
    min-height: 1px
    .slider-group
      position: relative
      overflow: hidden
      white-space: nowrap
      .slider-item
        float: left
        box-sizing: border-box
        overflow: hidden
        text-align: center
        a
          display: block
          width: 100%
          overflow: hidden
          text-decoration: none
        img
          display: block
          width: 100%
    .dots
      position: absolute
      right: 0
      left: 0
      bottom: 12px
      text-align: center
      font-size: 0
      .dot
        display: inline-block
        margin: 0 4px
        width: 8px
        height: 8px
        border-radius: 50%
        background: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>
