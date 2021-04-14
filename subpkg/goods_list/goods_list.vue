<template>
  <view>
    <view class="goods-list">
      <block v-for="(item, i) in goodsList" :key="i">
        <!-- 为 my-goods 组件动态绑定 goods 属性的值 -->
        <my-goods :goods="item"></my-goods>
      </block>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          // 查询关键词
          query: '',
          // 商品分类Id
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示多少条数据
          pagesize: 10,
        },
        // 商品列表的数据
        goodsList: [],
        // 总数量，用来实现分页
        total: 0,
        // 是否正在请求数据
        isloading: false
      };
    },
    onLoad(options) {
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''
      this.getGoodsList()
    },
    methods: {
      // 获取商品列表数据的方法
      async getGoodsList() {
        this.isloading = true
        // 发起请求
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        this.isloading = false
        if (res.meta.status !== 200) return uni.$showMsg()
        // 为数据赋值
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
      }
    },
    onReachBottom() {
      if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据加载完毕！')
      // 判断是否正在请求其它数据，如果是，则不发起额外的请求
      if (this.isloading) return
      this.queryObj.pagenum += 1
      this.getGoodsList()
    }
  }
</script>

<style lang="scss">

</style>
