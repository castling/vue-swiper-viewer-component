<template>
  <div class="viewer">
    <GlobalEvents
      @keyup.right="next"
      @keyup.left="prev"
      @keyup.shift.right="last"
      @keyup.shift.left="first"
    />

    <CoolLightBox 
      v-show="initialized"
      :items="images" 
      :index="index"
      :useZoomBar="true"
      :fullScreen="true"
      :enableWheelEvent="true"
      @on-change="change($event+1)"
      @close="index = null"
    />

    <div class="swiper-container"
      @click="index=page-1">
      <div class="swiper-wrapper viewer__wrapper">
        <div class="swiper-slide viewer__slide" v-for="(img,i) in images" :key="i"
          :class="{ 'no-image': noImage[i] || images==null || images.length==0 }"
          >
          <img :src="img" class="viewer__image"
            @error="setNoImage(i,true)"
            @success="setNoImage(i,false)"
          >
<!--
          <div class="swiper-lazy-preloader swiper-lazy-preloader-white"></div>
-->
        </div>
      </div>
      <div class="swiper-scrollbar"></div>
      <div class="swiper-button-next" v-show="showSwitch" @click="next"></div>
      <div class="swiper-button-prev" v-show="showSwitch" @click="prev"></div>
    </div>
  </div>
</template>

<script>
import GlobalEvents from 'vue-global-events'
import Swiper from 'swiper'
import CoolLightBox from 'vue-cool-lightbox'
import 'vue-cool-lightbox/dist/vue-cool-lightbox.min.css'

export default {
  components: {
    GlobalEvents,
    CoolLightBox,
  },
  props: {
    page: Number,
    images: Array,
    showSwitch: Boolean,
  },
  data() {
    return {
      swiperOptions: {
        direction: 'horizontal',
//        lazy: true,
        loop: false,
        spaceBetween: 30,
        scrollbar: {
          el: '.swiper-scrollbar',
        },
        keyboard: {
          enabled: false,
          onlyInViewport: true,
        },
      },
      noImage: [],
      index: null,
      initialized: false,
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
//      this.swiper.on('slideChange',() => {
//        this.$emit('update:page',this.swiper.activeIndex+1)
//      })
      this.swiper.init()
      this.initialized = true
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
    setNoImage(num,flg) {
      this.$set(this.noImage,num,flg)
    },

    next() {
      if( this.index==null ) {
        this.change(this.page+1)
      }
    },
    prev() {
      if( this.index==null ) {
        this.change(this.page-1)
      }
    },
    last() {
      if( this.index==null ) {
        this.change(this.images?this.images.length:0)
      }
    },
    first() {
      if( this.index==null ) {
        this.setPage(1)
      }
    },

    change(num) {
      this.$emit('change',num)
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

  .viewer__slide.no-image {
    height: 100%;
    min-height: 300px;
    &::after {
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
    .viewer__image {
      display: none;
    }
  }

  .swiper-container {
    width: 100%;
    height: 100%;
    cursor: pointer;
    &__wrapper {
      align-items: center;
    }
  }
  &__image {
    object-fit: contain;
    width: 100%;
    height: 100%;
    max-height: 600px;
    &.vertical {
      height: auto;
    }
  }
}
</style>
