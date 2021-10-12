<template>
  <div class="header-container">
    <div>
      <i
        :class="{
          'el-icon-s-fold': collapse,
          'el-icon-s-unfold': !collapse
        }"
        @click="collapse = !collapse"
      ></i>
      <span>江苏传智播客科技教育有限公司</span>
    </div>
    <el-dropdown>
      <div class="avatar-wrap">
        <img class="avatar" :src="user.photo" alt="" />
        <span>{{ user.name }}</span>
        <i class="el-icon-arrow-down el-icon--right"></i>
      </div>
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item>设置</el-dropdown-item>
        <el-dropdown-item @click.native="onLogout">退出</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
  </div>
</template>

<script>
import request from '@/utils/request'
export default {
  name: 'AppHeader',
  components: {},
  props: {
    collapse: {
      type: Boolean,
      require: true
    }
  },
  data () {
    return {
      user: {}
    }
  },
  computed: {},
  watch: {},
  async created () {
    request({
      method: 'GET',
      url: '/mp/v1_0/user/profile'
      // 后端要求把需要授权的用户身份放到请求头中
      // axios 可以通过 headers 选项设置请求头
      // headers: {
      //   // 属性名和值都得看接口的要求
      //   // 属性名：Authorization，接口要求的
      //   // 属性值：Bearer空格token数据
      //   Authorization:
      //     'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MTg5MDkxMjYsInVzZXJfaWQiOjEsInJlZnJlc2giOmZhbHNlLCJ2ZXJpZmllZCI6dHJ1ZX0.EdKErKDqMc3snkYxqt02jSa8t9G44002yWKY3CMOMJg'
      // }
    })
      .then(res => {
        console.log(res)
        this.user = res.data.data
      })
      .catch(res => console.log(res))
  },
  mounted () {},
  methods: {
    onLogout () {
      this.$confirm('确认退出吗？', '退出提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
        .then(() => {
          // 把用户的登录状态清除
          window.localStorage.removeItem('user')

          // 跳转到登录页面
          this.$router.push('/login')
        })
        .catch(() => {
          this.$message({
            type: 'info',
            message: '已取消退出'
          })
        })
    }
  }
}
</script>

<style scoped lang="less">
.header-container {
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ccc;
  .avatar-wrap {
    display: flex;
    align-items: center;
    .avatar {
      width: 30px;
      height: 30px;
      border-radius: 50%;
      margin-right: 10px;
    }
  }
}
</style>
