<template>
  <div class="index">
    <!-- 搜索 -->
    <form class="search" action="/">
      <van-search
        v-model="search.key"
        :show-action="search.showAction"
        placeholder="请输入搜索关键词"
        @search="onSearch"
        @cancel="onCancel"
      />
    </form>
    <!-- 搜索 -->
    <div class="banner">
      <!-- 小程序banner -->
      <wx-swiper check-wx-reduce class="swipe" :indicator-dots="banner.indicatorDots"
        :autoplay="banner.autoplay" :interval="banner.interval" :duration="banner.duration">
        <wx-swiper-item class="swiper-item" v-for="(item, index) in banner.list" v-bind:key="index">
          <wx-image :src="item.img" lazy-load="true" class="pic" mode="aspectFill"></wx-image>
        </wx-swiper-item>
      </wx-swiper>
      <!-- 小程序banner -->
      <!-- web banner -->
      <van-swipe check-reduce class="swipe" :autoplay="banner.interval"
      :duration="banner.duration">
        <van-swipe-item class="swiper-item" v-for="(item, index) in banner.list" v-bind:key="index">
        <!-- <img v-lazy="item.img" /> -->
        <van-image  width="100%" height="100%" lazy-load :src="item.img" fit="cover">
          <template v-slot:loading>
            <van-loading type="spinner" size="20" />
          </template>
        </van-image>
        </van-swipe-item>
      </van-swipe>
      <!-- web banner -->
    </div>
    <div class="fast-nav">
      <div class="container">
        <van-row type="flex" justify="space-between">
          <van-col v-for="(item, index) in fast_nav" v-if="index < 4" v-bind:key="index" span="4">
            <a class="fast-item" href="">
              <wx-image v-if="type == 1"
              :src="item.icon" lazy-load="true"
              class="icon" mode="aspectFit"></wx-image>
              <van-image check-reduce class="icon" :src="item.icon" lazy-load fit="cover">
                <template v-slot:loading>
                  <van-loading type="spinner" size="20" />
                </template>
              </van-image>
              <div class="text">{{item.title}}</div>
            </a>
          </van-col>
        </van-row>
        <van-row type="flex" justify="space-between">
          <van-col v-for="(item, index) in fast_nav" v-if="index > 3" v-bind:key="index" span="4">
            <a class="fast-item" href="">
              <wx-image v-if="type == 1"
              :src="item.icon" lazy-load="true"
              class="icon" mode="aspectFit"></wx-image>
              <van-image check-reduce class="icon" :src="item.icon" lazy-load fit="cover">
                <template v-slot:loading>
                  <van-loading type="spinner" size="20" />
                </template>
              </van-image>
              <div class="text">{{item.title}}</div>
            </a>
          </van-col>
        </van-row>
      </div>
    </div>
    <van-list class="home-list"
    v-model="loading"
    :finished="finished"
    finished-text="没有更多了"
    @load="onLoad">
      <van-cell v-for="item in list" :key="item" :title="item" />
    </van-list>
    <Footer></Footer>
  </div>
</template>

<script>
import Vue from 'vue'
import Footer from '../../components/common/Footer.vue'
import { Toast, Lazyload, List } from 'vant'
import Web from 'reduce-loader!../../components/common/Web.vue'
import 'reduce-loader!./web'
import { getIndex } from '../../api/home'

Vue.use(Lazyload)
Vue.use(List)
export default Vue.extend({
  name: 'Home',
  components: {
    Footer,
    Web
  },
  data() {
    return {
      type: 0,
      search: {
        showAction: false,
        key: ''
      },
      banner: {},
      fast_nav: [],
      list: [],
      loading: false,
      finished: false
    }
  },
  created() {
    const that = this
    getIndex
      .then((res) => {
        that.banner = res.data.data.banner
        that.fast_nav = res.data.data.fast_nav
      })
      .catch(() => {})
    window.addEventListener('wxload', query =>
      console.log('page1 wxload', query)
    )
    window.addEventListener('wxshow', () => console.log('page1 wxshow'))
    window.addEventListener('wxready', () => console.log('page1 wxready'))
    window.addEventListener('wxhide', () => console.log('page1 wxhide'))
    window.addEventListener('wxunload', () => console.log('page1 wxunload'))

    if (process.env.isMiniprogram) {
      this.type = 1
      console.log('I am in miniprogram')
    } else {
      console.log('I am in Web')
    }
  },
  methods: {
    onSearch(val) {
      Toast(val)
    },
    onCancel() {
      Toast('取消')
    },
    onLoad() {
      // 异步更新数据
      // setTimeout 仅做示例，真实场景中一般为 ajax 请求
      setTimeout(() => {
        for (let i = 0; i < 10; i += 1) {
          this.list.push(this.list.length + 1)
        }

        // 加载状态结束
        this.loading = false

        // 数据全部加载完成
        if (this.list.length >= 40) {
          this.finished = true
        }
      }, 1000)
    }
  }
})
</script>

<style lang="scss">
.wx {
  display: none;
}
.index {
  padding: 0.54rem 0 0.6rem;
  .search {
    position: fixed;
    width: 100%;
    z-index: 10;
    top: 0;
    left: 0;
    .van-search {
      padding: 0.1rem 0.12rem;
      .van-search__content {
        padding-left: 0.08rem;
        border-radius: 0.02rem;
        .van-cell {
          padding: 0.05rem 0.08rem 0.05rem 0;
          .van-field__left-icon {
            line-height : 0.24rem;
            font-size: 0.16rem;
            .van-icon{
              font-size: 0.16rem;
            }
          }
          .van-field__control {
            height: 0.24rem;
            min-height: 0.24rem;
            line-height : 0.24rem;
            font-size: 0.14rem;
          }
        }
      }
    }
  }
  .banner {
    height: 2rem;
    .swipe{
      height: 100%;
      .swiper-item {
        color: #fff;
        font-size: 20px;
        height: 100%;
        text-align: center;
        background-color: #F7F7F7;
        background-size: cover;
        background-position: center;
        .pic{
          width: 100%;
          height: 100%;
        }
      }
    }
  }
  .fast-nav {
    background-color: #fff;
    font-size: 0.16rem;
    .fast-item{
      font-size: 0.14rem;
      border: none;
      text-align: center;
      display: block;
      line-height: 1em;
      height: auto;
      margin-bottom: 1em;
      .icon {
        width: 3.5em;
        height: 3.5em;
        display: block;
        margin: 1em auto;
      }
    }
  }
}
.home-list {
  margin-top: 0.15rem;
}
.miniprogram-root {
  .for-web {
    display: none;
  }
}
</style>
