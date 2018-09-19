<template>
<div>
  <BookInfo :info='info'></BookInfo>
</div>
  
</template>
<script>
import BookInfo from '@/components/BookInfo'
export default {
  components: {
    BookInfo
  },
  data () {
    return {
      info: {}
    }
  },
  mounted () {
    let bookid = this.$root.$mp.query.id
    let book = wx.getStorageSync('books')
    for (let i = 0; i < book.length; i++) {
      if (book[i].id === bookid) {
        this.info = book[i]
        wx.setNavigationBarTitle({
          title: book[i].title
        })
      }
    }
  }
}
</script>
<style lang='scss'>
.comment{
  margin-top:10px;
  .textarea{
    width:730rpx;
    height:200rpx;
    background:#eee;
    padding:10rpx;
  }
  .location{
    margin-top:10px;
    padding:5px 10px;
  }
  .phone{
    margin-top:10px;
    padding:5px 10px;
    
  }
}

</style>

