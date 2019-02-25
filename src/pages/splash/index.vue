<template>
  <div class="container">
  <swiper class="splash" indicator-dots>
    <swiper-item class="sw-item" v-for="(item,index) in movies" :key="index">
      <image :src="item.image" class="slide-image" mode="aspectFill"/>
      <button class="start" v-if="index == count-1" @tap="board">立即体验</button>
    </swiper-item>
  </swiper>
</div>

</template>

<script>
import douban from "../../utils/douban.js"
export default {
  data () {
    return {
      movies: [
      // {image:'',id:xx}
    ],
    count:3
    }
  },
  methods:{
    board(){
        mpvue.setStorageSync('splash_douban',true);

        mpvue.switchTab({
          url: '/pages/board/main'
        });

      },
  },
 onLoad() {

    let value = mpvue.getStorageSync('splash_douban')

    if (value) {
      mpvue.switchTab({
        url: '/pages/board/main'
      })
      return;
    }


    douban({
      url: '/v2/movie/coming_soon',
      data: {
        start:1,
        count:this.count
      }
    }).then(
      res=>{
        if (!res.data.subjects) return;
        let result = [];
        res.data.subjects.map((item) => {
          result.push({
            image: item.images.large,
            id: item.id
          })
        });
        this.movies=result;
        // this.setData({ movies:result});
      }
    )
    // wx.request({
    //   url: app.globalData.baseUrl +'/v2/movie/coming_soon',
    //   data:{
    //     start:1,
    //     count:this.data.count
    //   },
    //   header:{
    //     'content-type':'json'
    //   },
    //   success:function(res){
    //     if (!res.data.subjects) return;
    //     let result = [];
    //     res.data.subjects.map((item) => {
    //       result.push({
    //         image: item.images.large,
    //         id: item.id
    //       })
    //     });

    //     _this.setData({ movies:result});
    //   }
    // })
  },
  created () {
    // let app = getApp()
  }
}
</script>

<style scoped>
.container {
  height: 100%;
  overflow: hidden;
}

.splash {
  height: 100%;
}

.splash sw-item {
  flex: 1;
}

.splash .slide-image {
  position: absolute;
  height: 100%;
  width: 100%;
  opacity: 0.9;
}

.start {
  position: absolute;
  bottom: 200rpx;
  left: 50%;
  width: 300rpx;
  margin-left: -150rpx;
  background-color: rgba(53, 73, 94, 0.4);
  color: #fff;
  border: 5rpx solid #35495e;
  font-size: 40rpx;
}

</style>
