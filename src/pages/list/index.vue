<template>
  <div class="container">
  <div class="list">
    <navigator 
      v-for="item in list"
      :key="item.id"
      :url="'../item/main?id='+item.id"
    >
      <product :item="item"></product>
    </navigator>
    
  </div>
</div>

</template>

<script>
import douban from '../../utils/douban.js'
import product from "@/components/product.vue"
export default {
  data () {
    return {
      title: '数据加载中',//页面第一次渲染用到的值
      type: '',
      page: 1, //页码
      size: 10,//页数
      list: [
        // {id:'', image: '', title: '', average: 4.3, original_title: '', year: '', directors:''}
      ]
      
    }
  },
  components:{
      product
  },
methods:{
   loadList: function () {
      douban({
        url: '/v2/movie/' + this.type,
        data: {
          count: this.size,
          start: this.page++
        },
        loading: true
      }).then(
        res => {
          if (!res.data.subjects) return;
          let result = [];
          res.data.subjects.map((item) => {
            result.push({
              image: item.images.small,
              id: item.id,
              title: item.title,
              average: item.rating.average,
              original_title: item.original_title,
              year: item.year,
              directors: item.directors[0].name
            })
          });
          this.list=result;
          this.title=res.data.title;
          // this.setData({
          //   list: result,
          //   title: res.data.title//渲染list时需要在渲染一次title
          // });

          // console.log(this.list)
        }
      )
  }
},
  
  beforeMount: function () {
    this.title = this.$root.$mp.query.title || this.title

    // 类型： in_theaters  coming_soon  us_box
    this.type = this.$root.$mp.query.key || this.type
    this.loadList();
  },
  mounted: function () {
    mpvue.setNavigationBarTitle({
      title: this.title + ' « 电影 « 豆瓣'
    });
  }
}
</script>

<style scoped>
.container {
  display: flex;
  flex: 1;
  flex-direction: column;
  box-sizing: border-box;
}

.list {
  display: flex;
  flex: 1;
  flex-direction: column;
}


</style>
