<template>
  <el-card style="height: 1200px;">
    <el-input placeholder="请输入内容" suffix-icon="el-icon-search" v-model="searchPage.s"
      style="width: 300px;margin-left: 58px;" @change="searchBook">
    </el-input>
    <el-pagination background layout="prev, pager, next" :total="30" :current-page="currentPage"
      @current-change="pushPage" style=" position: absolute;left: 50%;top:120px;transform: translate(-30%, -50%);">
    </el-pagination>
    <el-row>
      <!-- span是卡片的宽度 offset是-->
      <el-col :span="3" v-for="book in books" :key="book.id" :offset="1">
        <el-card :body-style="{ padding: '10px',width: '200px'}" class="margin">
          <div style="padding: 14px;">
            <strong style="font-size: 15px;">《{{book.bookname}}》</strong>
            <p>数量：{{book.count}}</p>
            <p>{{book.pub}}</p>
            <p>{{book.kind}}</p>
            <div class="bottom clearfix">
              <el-button type="text" class="button" @click="borrowBooks(book.id)">借阅</el-button>
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>

  </el-card>
</template>
<style>
  .margin {
    margin-top: 20px;
  }

</style>
<script>
  export default {
    data() {
      return {
        /* username登录后获取复制到data渲染到页面上 */
        username: "陈小虎",
        /* admin判断是否有权限 */
        admin: false,
        /* 导航组件 */
        activeIndex2: '100',
        /* 用来分页的 */
        page: {
          "currentPage": "1",
          "pageSize": "12"
        },
        /* 赋值渲染书籍列表 */
        books: [],
        /* 搜索框 */
        searchPage: {
          currentPage: "1",
          pageSize: "15",
          s: ""
        },
        currentPage: 1
      }
    },
    created() {
      this.init()
    },
    methods: {
      async init() {
        const {
          data: res
        } = await this.$http.post('book/getBook', this.page)
        this.books = res.data
        console.log(this.books)
      },
      async borrowBooks(id) {
        console.log(id)
        const {
          data: res
        } = await this.$http.post('book/Borrow', {
          "id": id
        })
        if (res.code !== 200) {
          this.$message.error("借书失败")
        }
        this.$message.success("借书成功")
        this.init()
      },
      async searchBook() {
        const {
          data: res
        } = await this.$http.post('book/queryBook', this.searchPage)
        if (res.data.length === 0) {
          this.$message.error("查询失败")
        }
        console.log(this.searchPage.s)
        console.log(res)
        console.log(res.data.length)
        this.books = res.data

      },
      async pushPage(currentPage) {
        console.log(currentPage)
        this.page.currentPage = currentPage
        const {
          data: res
        } = await this.$http.post('book/getBook', this.page)
        console.log(res)
        this.books = res.data
      }
    }
  }

</script>
