<template>
  <div class="menu-bar">
    <transition name="slide-up">
      <div class="menu-wrapper" :class="{'hide-box-show': isSettingShow || !isTitleAndMenuShow}" v-show="isTitleAndMenuShow">
        <div class="icon-wrapper">
          <span class="icon-uniE901 icon" @click="showSetting(3)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-5 icon" @click="showSetting(2)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-4 icon" @click="showSetting(1)"></span>
        </div>
        <div class="icon-wrapper">
          <span class="icon-a icon" @click="showSetting(0)">A</span>
        </div>
      </div>
    </transition>
    <transition name=slide-up>
      <div class="setting-wrapper" v-show="isSettingShow">
        <div class="setting-font-size" v-if="showTag === 0">
          <div class="preview" :style="{fontSize: fontsizeList[0].fontsize + 'px'}">A</div>
          <div class="select">
            <div 
              class="select-wrapper" 
              v-for="(item, index) in fontsizeList" 
              :key="index"
              @click="setFontSize(item.fontsize)"
            >
              <div class="line"></div>
              <div class="point-wrapper">
                <div class="point" v-show="defaultFontSize === item.fontsize">
                  <div class="small-point"></div>
                </div>
              </div>
              <div class="line"></div>
            </div>
          </div>
          <div class="preview" :style="{fontSize: fontsizeList[fontsizeList.length - 1].fontsize + 'px'}">A</div>
        </div>
        <div class="setting-theme" v-else-if="showTag === 1">
          <div class="setting-theme-item" v-for="(item, index) in themeList" :key="index" @click="setTheme(index)">
            <div 
              class="preview" 
              :style="{background: item.style.body.background}" 
              :class="{'no-boder': item.style.body.background !== '#fff'}"
            ></div>
            <div class="text" :class="{'select': defaultTheme === index}">{{item.name}}</div>
          </div>
        </div>
        <div class="setting-progress" v-else-if="showTag === 2">
          <div class="progress-wrapper">
            <input 
              class="progress" 
              type="range"
              max="100"
              min="0"
              step="1"
              :value="progress"
              @input="onProgressInput"
              @change="onProgressChange(progress)"
              :disabled='!bookAvailable'
              ref="progress"
            >
          </div>
          <div class="text-wrapper">
            <span>{{bookAvailable ? progress + '%' : '加载中...'}}</span>
          </div>
        </div>
      </div>
    </transition>
    <content-view 
      :isShowContent="isShowContent"
      :navigation='navigation'
      :bookAvailable='bookAvailable'
      @jumpTo='jumpTo'
    />
    <transition name='fade'>
      <div 
        class="content-mask"
        v-show="isShowContent"
        @click="hideContent"
      ></div>
    </transition>
  </div>
</template>
<script>
import ContentView from "@/components/ContentView.vue";
export default {
  components: {
    ContentView
  },
  props: {
    isTitleAndMenuShow: {
      type: Boolean,
      default: false
    },
    fontsizeList: Array,
    defaultFontSize: Number,
    themeList: Array,
    defaultTheme: Number,
    bookAvailable: {
      type: Boolean,
      default: false
    },
    navigation: Object
  },
  data() {
    return {
      isSettingShow: false,
      showTag: 0,
      progress: 0,
      isShowContent: false
    };
  },
  methods: {
    showSetting(tag) {
      this.showTag = tag;
      if (this.showTag === 3) {
        this.isSettingShow = false;
        this.isShowContent = true;
      } else {
        this.isSettingShow = true;
      }
    },
    hideSetting() {
      this.isSettingShow = false;
    },
    setFontSize(fontSize) {
      this.$emit("setFontSize", fontSize);
    },
    setTheme(index) {
      this.$emit("setTheme", index);
    },
    onProgressInput(e) {
      const progress = e.target.value;
      this.progress = progress;
      this.$refs.progress.style.backgroundSize = `${this.progress}% 100%`;
    },
    onProgressChange(progress) {
      this.$emit("onProgressChange", progress);
    },
    hideContent() {
      this.isShowContent = false;
    },
    jumpTo(href) {
      this.$emit("jumpTo", href);
    }
  }
};
</script>
<style lang="scss" scoped>
@import "@/assets/styles/global.scss";

.menu-bar {
  .menu-wrapper {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    height: px2rem(40);
    z-index: 102;
    display: flex;
    background-color: #fff;
    box-shadow: 0 px2rem(-2) px2rem(2) rgba(0, 0, 0, 0.15);
    &.hide-box-show {
      box-shadow: none;
    }
    .icon-wrapper {
      flex: 1;
      @include center;
      &:hover {
        cursor: default;
      }
    }
  }
  .setting-wrapper {
    position: absolute;
    bottom: px2rem(40);
    left: 0;
    z-index: 101;
    width: 100%;
    height: px2rem(50);
    background-color: #fff;
    box-shadow: 0 px2rem(-2) px2rem(2) rgba(0, 0, 0, 0.15);
  }
  .setting-font-size {
    display: flex;
    height: 100%;
    .preview {
      flex: 0 0 px2rem(20);
      @include center;
    }
    .select {
      display: flex;
      flex: 1;
      .select-wrapper {
        flex: 1;
        display: flex;
        align-items: center;
        &:first-of-type {
          .line {
            &:first-of-type {
              border-top: none;
            }
          }
        }
        &:last-of-type {
          .line {
            &:last-of-type {
              border-top: none;
            }
          }
        }
        .line {
          flex: 1;
          height: 0;
          border-top: px2rem(1) solid #ccc;
        }
        .point-wrapper {
          flex: 0 0 0;
          width: 0;
          height: px2rem(7);
          border-left: px2rem(1) solid #ccc;
          position: relative;
          .point {
            width: px2rem(20);
            height: px2rem(20);
            background-color: #fff;
            border: px2rem(1) solid #ccc;
            border-radius: 50%;
            box-shadow: 0 px2rem(2) px2rem(4) rgba(0, 0, 0, 0.15);
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            @include center;
            .small-point {
              width: px2rem(5);
              height: px2rem(5);
              background-color: #000;
              border-radius: 50%;
            }
          }
        }
      }
    }
  }
  .setting-theme {
    display: flex;
    height: 100%;
    .setting-theme-item {
      flex: 1;
      display: flex;
      flex-direction: column;
      padding: px2rem(5);
      .preview {
        flex: 1;
        border: px2rem(1) solid #ccc;
        box-sizing: border-box;
        &.no-boder {
          border: none;
        }
      }
      .text {
        flex: 0 0 px2rem(15);
        font-size: px2rem(12);
        color: #ccc;
        margin-top: px2rem(4);
        @include center;
        &.select {
          color: #333;
        }
      }
    }
  }
  .setting-progress {
    display: flex;
    height: 100%;
    flex-wrap: wrap;
    .progress-wrapper {
      width: 100%;
      padding: px2rem(10) px2rem(30);
      box-sizing: border-box;
      @include center;
      .progress {
        width: 100%;
        height: px2rem(2);
        -webkit-appearance: none;
        background: -webkit-linear-gradient(#999, #999) no-repeat #ddd;
        background-size: 0 100%;
        &:focus {
          outline: none;
        }
        &::-webkit-slider-thumb {
          -webkit-appearance: none;
          width: px2rem(16);
          height: px2rem(16);
          background-color: #fff;
          border: px2rem(1) solid #ccc;
          border-radius: 50%;
          box-shadow: 0 px2rem(2) px2rem(4) rgba(0, 0, 0, 0.15);
        }
      }
    }
    .text-wrapper {
      width: 100%;
      font-size: px2rem(12);
      @include center;
    }
  }
  .content-mask {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 102;
    display: flex;
    width: 100%;
    height: 100%;
    background-color: rgba(51, 51, 51, 0.8);
  }
}
</style>
