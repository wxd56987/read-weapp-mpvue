<template>
  <div>
    <Card :key='book.id' v-for='book in books' :book='book'></Card>
    <p class='text-footer' v-if="more">没有更多了</p>
  </div>
</template>

<script>
import Card from '@/components/Card'
export default {
  components: {
    Card
  },
  data () {
    return {
      books: [],
      more: true
    }
  },
  onShow () {
    wx.setNavigationBarTitle({
      title: '图书列表'
    })
    let book = wx.getStorageSync('books')
    if (book) {
      this.books = book.reverse()
      this.more = false
    }
  }
}
</script>

<style>

</style>

