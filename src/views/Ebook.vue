<template>
  <div class='ebook'>
    <title-bar :isTitleAndMenuShow="isTitleAndMenuShow"/>
    <div class="read-wrapper">
      <div id='read'></div>
      <div class="mask" @click="toggleTitleAndMenu">
        <div class="left" @click="prevPage"></div>
        <div class="center"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar 
      :isTitleAndMenuShow="isTitleAndMenuShow"
      :fontsizeList='fontsizeList'
      :defaultFontSize='defaultFontSize'
      :themeList="themeList"
      :defaultTheme="defaultTheme"
      :bookAvailable="bookAvailable"
      :navigation='navigation'
      @setFontSize='setFontSize'
      @setTheme="setTheme"
      @onProgressChange="onProgressChange"
      @jumpTo='jumpTo'
      ref="menuBar"
    />
  </div>
</template>
<script>
import Epub from "epubjs";
import TitleBar from "@/components/TitleBar";
import MenuBar from "@/components/MenuBar";
const DOWNLOAD_URL = "/book/167176.epub";
export default {
  components: {
    TitleBar,
    MenuBar
  },
  data() {
    return {
      isTitleAndMenuShow: false,
      fontsizeList: [
        { fontsize: 12 },
        { fontsize: 14 },
        { fontsize: 16 },
        { fontsize: 18 },
        { fontsize: 20 },
        { fontsize: 22 },
        { fontsize: 24 }
      ],
      defaultFontSize: 16,
      themeList: [
        {
          name: "default",
          style: {
            body: {
              color: "#000",
              background: "#fff"
            }
          }
        },
        {
          name: "eye",
          style: {
            body: {
              color: "#000",
              background: "#ceeaba"
            }
          }
        },
        {
          name: "night",
          style: {
            body: {
              color: "#fff",
              background: "#000"
            }
          }
        },
        {
          name: "gold",
          style: {
            body: {
              color: "#000",
              background: "rgba(241, 236, 226)"
            }
          }
        }
      ],
      defaultTheme: 0,
      bookAvailable: false,
      navigation: null
    };
  },
  methods: {
    showEpub() {
      this.book = Epub(DOWNLOAD_URL);
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight
      });
      // 通过rendition.display获取电子书
      this.rendition.display();
      // 获取theme对象
      this.themes = this.rendition.themes;
      // 设置默认字体
      this.setFontSize(this.defaultFontSize);
      this.registTheme();
      this.setTheme(this.defaultTheme);
      // 获取locations对象
      // 通过epubjs的钩子函数来实现
      this.book.ready
        .then(() => {
          this.navigation = this.book.navigation;
          return this.book.locations.generate();
        })
        .then(() => {
          this.locations = this.book.locations;
          this.bookAvailable = true;
          // this.onProgressChange(50);
        });
    },
    prevPage() {
      if (this.rendition) {
        this.isTitleAndMenuShow = true;
        this.rendition.prev();
        console.log(this.rendition);
      }
    },
    nextPage() {
      if (this.rendition) {
        this.isTitleAndMenuShow = true;
        this.rendition.next();
        console.log(this.rendition);
      }
    },
    toggleTitleAndMenu() {
      this.isTitleAndMenuShow = !this.isTitleAndMenuShow;
      if (!this.isTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting();
      }
    },
    setFontSize(fontSize) {
      this.defaultFontSize = fontSize;
      if (this.themes) {
        this.themes.fontSize(fontSize + "px");
      }
    },
    registTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style);
      });
    },
    setTheme(index) {
      this.themes.select(this.themeList[index].name);
      this.defaultTheme = index;
    },
    onProgressChange(progress) {
      const percentag = progress / 100;
      const location = percentag > 0 ? this.locations.cfiFromPercentage(percentag) : 0;
      this.rendition.display(location);
    },
    jumpTo(href) {
      this.rendition.display(href);
      console.log(this.rendition);
      this.hideTitleAndMenu();
    },
    hideTitleAndMenu() {
      this.isTitleAndMenuShow = false;
      this.$refs.menuBar.hideSetting();
      this.$refs.menuBar.hideContent();
    }
  },
  mounted() {
    this.showEpub();
  }
};
</script>
<style lang="scss" scoped>
@import "@/assets/styles/global.scss";

.ebook {
  position: relative;
  .read-wrapper {
    .mask {
      width: 100%;
      height: 100%;
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      display: flex;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>
