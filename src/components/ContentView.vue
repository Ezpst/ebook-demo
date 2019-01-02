<template>
  <transition name='slide-right'>
    <div class='content' v-show="isShowContent">
      <div class="content-wrapper" v-if="bookAvailable">
        <div class="content-item" 
          v-for="(item, index) in navigation.toc" 
          :key="index"
          @click="jumpTo(item.href)"
        >
          <span class="text">{{item.label}}</span>
        </div>
      </div>
      <div class="empty" v-else>加载中...</div>
    </div>
  </transition>
</template>
<script>
export default {
  props: {
    navigation: Object,
    bookAvailable: Boolean,
    isShowContent: Boolean
  },
  data() {
    return {};
  },
  methods: {
    jumpTo(href) {
      this.$emit("jumpTo", href);
    }
  }
};
</script>
<style lang="scss" scoped>
@import "@/assets/styles/global.scss";
.content {
  width: 75%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 103;
  overflow-y: auto;
  .content-wrapper {
    width: 100%;
    background-color: #fff;
    .content-item {
      display: flex;
      align-items: center;
      padding: px2rem(10);
      &:hover {
        background-color: #eee;
        cursor: pointer;
      }
      .text {
        font-size: px2rem(12);
        line-height: px2rem(20);
      }
    }
  }
  .empty {
    width: 100%;
    height: 100%;
    background-color: #fff;
    font-size: px2rem(14);
    @include center;
  }
}
</style>
