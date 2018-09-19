<template>
 <navigator :url="detailUrl">
  <div class="book-card">
    <div class="thumb" @click.stop="preview">
      <img :src="book.image" 
            class="img"
            mode="aspectFit"
            />
    </div>
    <div class="detail">
      <div class="row text-primary">
        <div class="right">
          {{book.rating.average}} <Rate :value='book.rating.average'></Rate>
        </div>
        <div class="left">
          {{book.title}}
        </div>
      </div>
      <div class="row">
        <div class="right text-primary">
          浏览量:{{book.rating.numRaters}}
        </div>
        <div class="left">
          {{book.author}}
        </div>
      </div>
      <div class="row">
        <div class="right">
           {{book.nickName}}
        </div>
        <div class="left">
         {{book.publisher}}
        </div>
      </div>


    </div>
  </div>
  </navigator>
</template>

<script>
import Rate from '@/components/Rate'
export default {
  components: {
    Rate
  },
  props: ['book'],
  methods: {
    preview () {
      wx.previewImage({
        current: this.book.image,
        urls: [this.book.image]
      })
    }
  },
  computed: {
    detailUrl () {
      return '/pages/detail/main?id=' + this.book.id
    }
  }
}
</script>

<style lang='scss' scoped>
.text-primary{
  color: #EA5149;
}
.book-card{
  padding:5px;
  overflow: hidden;
  margin-top:5px;
  margin-bottom:5px;
  font-size:14px;
  .thumb{
    width:90px;
    height:90px;
    float: left;
    margin:0 auto;
    overflow:hidden;
    .img{
      max-width: 100%;
      max-height: 100%;
    }
  }
  .detail{
    margin-left: 100px;
    .row{
      line-height:20px;
      margin-bottom:3px;
    }
    .right{
      float: right;
    }
    .left{
      margin-right:80px;
    }
  }
  
}
</style>
