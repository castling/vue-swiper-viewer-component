<template>
  <div class="viewer">
    <div class="swiper-container">
      <div class="swiper-wrapper viewer__wrapper">
        <div class="swiper-slide viewer__slide" v-for="(img,i) in images" :key="i">
          <img :data-src="img" class="swiper-lazy viewer__image">
          <div class="swiper-lazy-preloader swiper-lazy-preloader-white"></div>
        </div>
      </div>
      <div class="swiper-scrollbar"></div>
      <div class="swiper-button-next" v-show="showSwitch" @click="$emit('next')"></div>
      <div class="swiper-button-prev" v-show="showSwitch" @click="$emit('prev')"></div>
    </div>
  </div>
</template>

<script>
import Swiper from 'swiper'

export default {
  props: {
    page: Number,
    images: Array,
    showSwitch: Boolean,
  },
  data() {
    return {
      swiperOptions: {
        direction: 'horizontal',
        lazy: true,
        loop: false,
        spaceBetween: 30,
        scrollbar: {
          el: '.swiper-scrollbar',
        },
      },
    }
  },
  watch: {
    images: {
      async handler() {
        if( this.swiper ) {
          this.update()
        }
      },
      deep: true,
    },
    page(num) {
      if( num!=null && this.swiper ) {
        this.swiper.slideTo(num-1)
      }
    },
    showSwitch() {
      if( this.swiper ) {
        this.update()
      }
    },
  },
  mounted() {
    this.init()
  },
  beforeDestroy() {
    this.destroy()
  },
  methods: {
    init() {
      this.swiper = new Swiper(this.$el.querySelector('.swiper-container'), {
        ...this.swiperOptions,
        init: false,
      })
      this.swiper.on('slideChange',() => {
        this.$emit('update:page',swiper.activeIndex)
      })
      this.swiper.init()
    },
    destroy() {
      this.swiper.destroy()
    },
    async update() {
      await this.$nextTick()
      this.swiper.update()
      setTimeout(() => {
        this.swiper.update()
      },100)
    },
  },
}
</script>

<style src="swiper/css/swiper.css"></style>

<style scoped lang="scss">
.viewer {
  width: 100%;
  height: 100%;
  background-color: rgb(230,230,230);
  .swiper-container {
    width: 100%;
    height: 100%;
  }
  &__image {
    object-fit: contain;
    width: 100%;
    height: 100%;
    &.vertical {
      height: auto;
    }
  }
}
</style>
