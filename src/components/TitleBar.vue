<template>
  <transition name="slide-down">
    <div class="title-wrapper" v-show="isTitleAndMenuShow">
      <div class="left">
        <span class="icon-back icon"></span>
      </div>
      <div class="right">
        <div class="icon-wrapper">
          <span class="icon-2 icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-1 icon"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-- icon"></span>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  props: {
    isTitleAndMenuShow: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {};
  }
};
</script>
<style lang="scss" scoped>
@import "@/assets/styles/global.scss";

.title-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: px2rem(40);
  z-index: 101;
  display: flex;
  background-color: #fff;
  box-shadow: 0 px2rem(2) px2rem(2) rgba(0, 0, 0, 0.15);
  .left {
    flex: 0 0 px2rem(60);
    @include center;
  }
  .right {
    display: flex;
    flex: 1;
    justify-content: flex-end;
    .icon-wrapper {
      flex: 0 0 px2rem(40);
      @include center;
    }
  }
}
</style>
