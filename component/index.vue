<template>
  <div class="image-preview" @click="imgClick">
    <div class="preview-source" ref="previewSource">
      <slot></slot>
    </div>
    <div class="preview-container" :class="{'show':show}">
      <div class="top-bar">
        <span class="close" @click="close">关闭</span>
        <slot name="topBar"></slot>
      </div>
      <div class="swiper-container">
        <div class="swiper-wrapper">
          <div class="swiper-slide" v-for="(item, index) in imgSrcs" :key="index">
            <div class="swiper-zoom-container">
              <img previewImage :src="item.src" />
            </div>
          </div>
        </div>
        <!-- Add Pagination -->
        <div v-if="options.pagination" class="swiper-pagination swiper-pagination-white"></div>
        <!-- Add Navigation -->
        <template v-if="options.navigation">
          <div class="swiper-button-prev"></div>
          <div class="swiper-button-next"></div>
        </template>
        <div
          v-if="counter"
          class="imgCount"
        >{{swiper ? swiper.activeIndex + 1 : 0}}/{{imgSrcs.length}}</div>
      </div>
    </div>
  </div>
</template>
<script>
import Swiper from "swiper/js/swiper.min.js";
import "swiper/css/swiper.min.css";
export default {
  name: "VueImagePreview",
  props: {
    // asign img to preview, if asign is get true value, you need to give img tag a 'preview' attribute,
    // then component will include img that just have 'preview' attribute
    attrAsign: {
      type: Boolean,
      default: false
    },
    // update swiper way (manual operation or auto)
    // true: component will update swiper itself, when click img
    // false: you need to manual call updateImage() method by youself (like: this.$refs['preview'].updateImage(), 'preview' string is VueImagePreview component ref value ) in parent component
    // autoUpdate default value is false, get all images
    autoUpdate: {
      type: Boolean,
      default: true
    },
    // show counter number or not
    counter: {
      type: Boolean,
      default: true
    },
    // swiper options, for more infomation you can see swiper officail document
    // https://swiperjs.com/api/
    options: {
      type: Object,
      default: () => {
        return {};
      }
    }
  },
  data() {
    return {
      swiper: null,
      imgSrcs: [],
      show: false
    };
  },
  methods: {
    imgClick(e) {
      this.$emit("open");
      const target = e.target;
      // if target element isn't img element, then return
      if (target.tagName !== "IMG") {
        return;
      }
      this.autoUpdate && this.updateImage();
      this.show = true;
      this.$nextTick(() => {
        const index = this.imgSrcs.findIndex(item => target.src === item.src);
        // if this.swiper doesn't exsist, create it
        if (!this.swiper) {
          this.initSwiper();
        } else {
          this.swiper.update();
        }
        // show current index image
        this.swiper.slideTo(index, 0);
      });
    },
    close() {
      this.show = false;
      this.$emit("close");
    },
    initSwiper() {
      const defaultOptions = {
        zoom: true
      };
      const option = Object.assign(defaultOptions, this.options);
      this.swiper = new Swiper(".swiper-container", option);
    },
    updateImage() {
      this.$nextTick(() => {
        this.imgSrcs = [];
        const tag = this.attrAsign ? "img[preview]" : "img";
        let imgs = this.$refs["previewSource"].querySelectorAll(tag);
        // get img src array
        for (let img of imgs) {
          this.imgSrcs.push({
            src: img.src
          });
        }
      });
    }
  },
  mounted() {
    !this.autoUpdate && this.updateImage();
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.preview-container {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  background: #000;
}
.swiper-container {
  width: 100%;
  height: 100%;
}
.swiper-slide {
  overflow: hidden;
}
.preview-container {
  opacity: 0;
  transform: scale(0);
  transition: 0.3s ease-in-out 0s;
}
.preview-container.show {
  transform: scale(1);
  opacity: 1;
}
.top-bar {
  position: absolute;
  right: 0;
  top: 0;
  left: 0;
  z-index: 1024;
  padding: 12px;
  color: #fff;
  text-align: right;
}
.preview-container /deep/ .swiper-pagination-bullet {
  background: #999;
}
.imgCount {
  color: #fff;
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  text-align: left;
  padding: 12px;
  font-size: 14px;
}
</style>