<template>
  <div class="viewer">
    <div class="swiper-container">
      <div class="swiper-wrapper viewer__wrapper">
        <div class="swiper-slide viewer__slide" v-for="(img,i) in images" :key="i"
          :class="{ 'no-image': noImage || images==null || images.length==0 }"
          >
          <img :data-src="img" class="swiper-lazy viewer__image"
            @error="noImage = true"
            @success="noImage = true"
          >
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
        noImage: false,
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
        this.$emit('update:page',this.swiper.activeIndex+1)
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
  min-height: 200px;
  background-color: rgb(230,230,230);

  .viewer__slide.no-image::after {
    content: "NO PAGE";
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    background-color: rgba(0,0,0,0.15);
    display: flex;
    align-items: center;
    justify-content: center;
    font-family: Verdana;
    font-size: 2.5rem;
    font-weight: 800;
  }

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
