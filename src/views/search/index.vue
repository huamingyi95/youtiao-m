<template>
  <div class="search-container">
    <!-- 搜索栏 -->
    <!--
          Tips: 在 van-search 外层增加 form 标签，且 action 不为空，即可在 iOS 输入法中显示搜索按钮
         -->
    <form class="search-form" action="/">
      <van-search
        v-model="searchText"
        show-action
        placeholder="请输入搜索关键词"
        background="#3296fa"
        @search="onSearch"
        @cancel="onCancel"
        @focus="isResultShow=false"
      />
    </form>
    <!-- /搜索栏 -->

    <!-- 搜索结果 -->
    <search-result v-if="isResultShow" :search-text="searchText"  />
    <!-- /搜索结果 -->

    <!-- 联想建议 -->
    <search-suggestion
     v-else-if="searchText"
     :search-text="searchText"
     @search="onSearch"
     />
    <!-- 联想建议 -->

    <!-- 搜索历史记录 -->
    <search-history
    v-else
    :search-histories="searchHistories"
    @search="onSearch"
    @clear-search-histories="searchHistories = []"
    />
    <!-- /搜索历史记录 -->
  </div>
</template>
<script>
import SearchHistory from './components/search-history.vue'
import SearchResult from './components/search-result.vue'
import SearchSuggestion from './components/search-suggestion.vue'
// 导入本地存储封装方法
import { setItem, getItem } from '@/utils/storage'
export default {
  name: 'SearchPage',
  components: { SearchResult, SearchSuggestion, SearchHistory },
  props: {},
  data () {
    return {
      searchText: '', // 绑定输入框变量
      isResultShow: false,
      searchHistories: getItem('serach-histories') || []
    }
  },
  computed: {},
  watch: {
    searchHistories (val) {
    // 同步到本地存储
      setItem('serach-histories', val)
    }
  },
  created () {},
  methods: {
    // 确定事件  【键盘按下了回车键或者手机键盘按下了搜索键/完成键】
    onSearch (val) {
      // 更新文本框内容
      this.searchText = val
      // 存储搜索历史记录
      // 要求：不要有重复历史记录、最新的排在最前面
      const index = this.searchHistories.indexOf(val)
      if (index !== -1) {
        this.searchHistories.splice(index, 1)
      }
      this.searchHistories.unshift(val)
      // 显示搜索结果
      this.isResultShow = true
    },
    //   取消事件   【点击了取消按钮或者关闭图标】
    onCancel () {
      this.$router.back()
    }
  }
}
</script>
<style scoped lang="less">
.search-container {
  padding-top: 108px;
  .van-search__action {
    color: #fff;
  }
  .search-form {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 1;
  }
}
</style>
