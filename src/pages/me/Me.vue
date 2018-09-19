<template>
  <div class="container">
    <div class="userinfo">
      <img :src="userinfo.avatarUrl" alt="">
      <p v-if="userinfo.openId">{{userinfo.nickName}}</p>
      <button v-if="!userinfo.openId" open-type="getUserInfo" lang="zh_CN" @getuserinfo="doLogin">登陆</button>
    </div>
    <YearProgress></YearProgress>
    <div v-if="userinfo.openId" class="btn" @click="scanBook">添加图书</div>
  </div>
</template>

<script>
import config from '@/config'
import {post, showLoad, showModalTip, showSuccess} from '@/util'
import qcloud from 'wafer2-client-sdk'
import YearProgress from '@/components/YearProgress'
export default {
  components: {
    YearProgress
  },
  data () {
    return {
      userinfo: {
        avatarUrl: '../../../static/img/unlogin.png',
        nickName: '点击登录'
      }
    }
  },
  methods: {
    async addBook (isbn) {
      console.log(isbn)
      const res = await post('/weapp/addbook', {
        isbn,
        openid: this.userinfo.openId
      })
      console.log(res)
      showModalTip('添加成功', `${res.title}添加成功`)
    },
    async getBookInfo (isbn) {
      const res = await this.getBookJSON(isbn)
      let books = wx.getStorageSync('books')
      let info = res
      let uinfo = wx.getStorageSync('userinfo')
      info.nickName = uinfo.nickName
      info.uimg = uinfo.avatarUrl
      info.catalog = ''
      if (books !== '') {
        books = books.concat(info)
        wx.setStorageSync('books', books)
      } else {
        let b = []
        b.push(info)
        wx.setStorageSync('books', b)
      }
      // console.log(info)
    },
    getBookJSON (isbn) {
      let url = 'https://douban.uieee.com/v2/book/isbn/' + isbn
      return new Promise((resolve, reject) => {
        wx.request({
          data: {},
          header: {
            'content-type': 'application/x-www-form-urlencoded'
          },
          url: url,
          success: function (res) {
            if (res.statusCode === 200) {
              showSuccess('成功')
              resolve(res.data)
            } else {
              showModalTip('失败', res.data)
              reject(res.data)
            }
          }
        })
      })
    },
    doLogin: function (e) {
      const self = this
      let user = wx.getStorageSync('userinfo')
      showLoad('登陆中...')
      if (!user) {
        qcloud.setLoginUrl(config.loginUrl)
        qcloud.login({
          success: function (userInfo) {
            console.log('登录成功', userInfo)
            self.loginok = true
            wx.setStorageSync('userinfo', userInfo)
            self.userinfo = userInfo
            wx.hideLoading()
            showSuccess('登录成功')
          },
          fail: function (err) {
            console.log('登录失败', err)
            wx.hideLoading()
          }
        })
      } else {
        wx.hideLoading()
      }
    },
    scanBook () {
      wx.scanCode({
        success: (res) => {
          if (res.result) {
            console.log(res.result)
            let books = wx.getStorageSync('books')
            if (books !== '') {
              for (let i = 0; i < books.length; i++) {
                if (books[i].isbn13 === res.result) {
                  showModalTip('', `已存在请勿重复添加`)
                  return
                }
              }
            }
            // this.addBook('1220562')
            this.getBookInfo(res.result)
          }
        }
      })
    }
  },
  onShow () {
    wx.setNavigationBarTitle({
      title: '我的'
    })
    let user = wx.getStorageSync('userinfo')
    if (user) {
      this.userinfo = user
    }
  }
}
</script>

<style lang="scss" scoped>
.container{
  padding:0 30rpx;
  text-align: center;
  .btn{
    display: inline-block;
    padding: 10rpx;
    color: #fff;
    background: #EA5149;
    margin: 60rpx auto;
    font-size: 30rpx;
    border-radius: 10rpx;
  }
}  
.userinfo{
  margin-top:100rpx;
  text-align:center;
  margin-bottom: 30rpx;
  img{
    width: 150rpx;
    height:150rpx;
    margin: 20rpx;
    border-radius: 50%;
  }
}
</style>

