<template>
  <div class="login-container">
    <!--
      el-form 表单组件
      每个表单项都必须使用 el-form-item 组件包裹
     -->

    <div class="login-form-wrap">
      <div class="login-head">
        <div class="logo"></div>
      </div>
      <!-- <el-form class="login-form" ref="form" :model="user"> -->
      <el-form
        class="login-form"
        :model="ruleForm"
        :rules="rules"
        ref="ruleForm"
      >
        <!-- <el-form-item>
          <el-input v-model="user.mobile" placeholder="请输入手机号"></el-input>
        </el-form-item> -->
        <el-form-item prop="mobile">
          <el-input v-model="ruleForm.mobile" placeholder="请输入手机号">
          </el-input>
        </el-form-item>

        <el-form-item prop="code">
          <el-input
            v-model="ruleForm.code"
            placeholder="请输入验证码"
          ></el-input>
        </el-form-item>
        <el-form-item prop="agree">
          <el-checkbox v-model="checked"
            >我已阅读并同意用户协议和隐私条款</el-checkbox
          >
        </el-form-item>
        <el-form-item>
          <el-button class="login-btn" type="primary" @click="onSubmit"
            >登录</el-button
          >
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
// import request from '@/utils/request'
import { Login } from '../../api/user'
export default {
  name: 'LoginIndex',
  components: {},
  props: {},
  data () {
    return {
      loading: false,
      ruleForm: {
        mobile: '13911111111', // 手机号
        code: '246810' // 验证码
      },
      checked: false, // 是否同意协议的选中状态
      rules: {
        mobile: [
          { required: true, message: '请输入手机号', trigger: 'change' },
          {
            pattern: /^1[3|5|7|8|9]\d{9}$/,
            message: '请输入正确的号码格式',
            trigger: 'change'
          }
        ],
        code: [
          { required: true, message: '验证码不能为空', trigger: 'change' },
          { pattern: /^\d{6}$/, message: '请输入正确的验证码格式' }
        ],
        agree: [
          {
            // 自定义校验规则：https://element.eleme.cn/#/zh-CN/component/form#zi-ding-yi-xiao-yan-gui-ze
            // 验证通过：callback()
            // 验证失败：callback(new Error('错误消息'))
            validator: (rule, value, callback) => {
              //   console.log(rule, value, callback)
              if (!value) {
                callback()
              } else {
                callback(new Error('请同意用户协议'))
              }
            },
            // message: '请勾选同意用户协议',
            trigger: 'change'
          }
        ]
      }
    }
  },
  computed: {},
  watch: {},

  mounted () {},
  methods: {
    onSubmit () {
      this.$refs.ruleForm.validate(valid => {
        if (!valid) {
          return
        }
        // 验证通过 登录
        this.login()
      })
    },
    login () {
      console.log(this.$message)
      // 获取表单数据（根据接口要求绑定数据）
      //   const ruleForm = this.ruleForm
      // 开启登陆中 loading...
      this.loginLoading = true

      // 表单验证

      // 验证通过，提交登录
      Login(this.ruleForm)
        .then(res => {
          console.log(res)
          // 登录成功
          this.$message({
            message: '登录成功',
            type: 'success'
          })
          window.localStorage.setItem('user', JSON.stringify(res.data.data))
          // 跳转到首页
          this.$router.push({
            name: 'home'
          })
          // 关闭 loading
          this.loginLoading = false
          // 登录成功
        })
        .catch(err => {
          console.log('登录失败', err)
          // 登录失败
          this.$message.error('登录失败，手机号或验证码错误')
          // 关闭 loading
          this.loginLoading = false
        })
    }
  }
}
</script>

<style scoped lang="less">
.login-container {
  position: fixed;
  left: 0;
  top: 0;
  right: 0;
  bottom: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: url('./login_bg.jpg') no-repeat;
  background-size: cover;
  .login-form-wrap {
    min-width: 300px;
    padding: 30px 50px 10px;
    background-color: #fff;
    .login-head {
      display: flex;
      justify-content: center;
      .logo {
        width: 200px;
        height: 57px;
        background: url('./logo_index.png') no-repeat;
        background-size: contain;
      }
    }
    .login-form {
      .login-btn {
        width: 100%;
      }
    }
  }
}
</style>
