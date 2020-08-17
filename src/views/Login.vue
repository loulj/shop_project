<template>
  <div class="login">
      <!-- 中央的盒子 -->
      <div class="login_box">
        <!-- 头像区域 -->
        <div class="avatar">
          <img src="../assets/logo.png" alt="">
        </div>
        <!-- 表单区域 ref可以拿到表单实例对象-->
        <el-form ref="loginFormRef" class="el-form" :model="loginForm" :rules="loginFormRules">
          <!-- 用户名 -->
          <el-form-item prop="username">
            <el-input prefix-icon="iconfont icon-user" v-model="loginForm.username"></el-input>
          </el-form-item>
          <!-- 密码 -->
          <el-form-item prop="password">
            <el-input prefix-icon="iconfont icon-3702mima" v-model="loginForm.password" type="password"></el-input>
          </el-form-item>
          <!-- 登录/重置 按钮 -->
          <el-form-item class="botton_btn">
            <el-button type="primary" @click="login">登录</el-button>
            <el-button type="info" @click="resetLoginForm">重置</el-button>
          </el-form-item>
        </el-form>
      </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      loginForm: {
        username: 'admin',
        password: '123456'
      },
      loginFormRules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 2, max: 8, message: '长度在 2 到 8 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 15, message: '长度在 6 到 15 个字符', trigger: 'blur' }
        ]
      }
    }
  },

  methods: {
    resetLoginForm () {
    // console.log(this)
      this.$refs.loginFormRef.resetFields()
    },
    login () {
      this.$refs.loginFormRef.validate(async valid => {
        if (!valid) return
        // console.log(this.$http)
        const { data: res } = await this.$http.post('login', this.loginForm)
        if (res.meta.status !== 200) return this.$message.error('登录失败!')
        this.$message.success('登录成功!')
        window.sessionStorage.setItem('token', res.data.token)
        this.$router.push('home')
      })
    }
  }
}
</script>

<style scoped lang='less'>
.login{
  /* 这设置100宽高,依据其父盒子大小,父盒子是加载的页面--app.vue;
    这个文件又加载到index.html中,
    所以index.html的body跟html以及#app都要设置宽高 */
   background: #2b4b6b;
   width: 100%;
   height: 100%;
  // 中央的盒子
   .login_box {
       width: 450px;
       height: 300px;
       background: #fff;
       border-radius: 10px;
       position: absolute;
       top: 50%;
       left: 50%;
       transform: translate(-50%,-50%);
      // 头像区域
       .avatar {
         width: 130px;
         height: 130px;
         border: 1px solid #eee;
         border-radius: 50%;
         padding: 10px;
         box-shadow: 0px 0px 10px #fff;
         position: absolute;
         left: 50%;
         transform: translate(-50%);
         background: #fff;
         top: -70px;

         img {
           width: 100%;
           height: 100%;
           margin-top: 15px;
         }
       }
      // 表单区域
      .el-form{
        margin-top: 100px;
        padding: 0 20px;
        .botton_btn{
          display: flex;
          justify-content: flex-end;
        }
      }
   }
}
</style>
