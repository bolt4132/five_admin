<template>
  <div class="login-container">
    <el-form ref="loginForm" :model="loginForm" :rules="loginRules" class="login-form" auto-complete="on" label-position="left">

      <div class="title-container">
        <h3 class="title">登录</h3>
      </div>

      <el-form-item prop="loginName">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          ref="username"
          v-model="loginForm.loginName"
          placeholder="用户名"
          name="username"
          type="text"
          tabindex="1"
          autocomplete="off"
        />
      </el-form-item>
      <el-form-item prop="pwd">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          :key="passwordType"
          ref="password"
          v-model="loginForm.pwd"
          :type="passwordType"
          placeholder="密码"
          name="password"
          tabindex="2"
          autocomplete="off"
          @keyup.enter.native="handleLogin('loginForm')"
        />
        <span class="show-pwd" @click="showPwd">
          <svg-icon :icon-class="passwordType === 'password' ? 'eye' : 'eye-open'" />
        </span>
      </el-form-item>
      
        <div class="forPwclass">
           <el-button :loading="loading" type="text"  @click="goResetPassword">修改密码</el-button>
          <el-button :loading="loading" type="text" style="font-size:14px" @click.native.prevent="handleReset">重置密码</el-button>
        </div>
       

      <el-button :loading="loading" type="primary" style="width:100%;margin-bottom:30px;" @click.native.prevent="handleLogin('loginForm')">登录企业信息填报系统</el-button>
      <div>
              <el-button :loading="loading" type="primary" style="width:100%;margin-bottom:30px;" @click.native.prevent="handleLoginDashbord('loginForm')">登录可视化平台</el-button>
      </div>
      
      <div class="regBox">
        <div>
          <span style="font-size:14px;color:#ffffff">需要帮助，点击</span>
           <el-tooltip class="item" effect="dark" content="填报之前，请仔细阅读" placement="top">
                
                <el-button :loading="loading" type="text" style="font-size:14px" @click.native.prevent="handleHelp">帮助</el-button>
          </el-tooltip>
         
        </div>
        <div>
          <span style="font-size:14px;color:#ffffff">没有账号，点击</span>
          <el-button :loading="loading" type="text" style="font-size:14px" @click.native.prevent="handleRegister">注册</el-button>
        </div>
        
      </div>
      <div class="downloadClass">
        <a href="https://www.google.cn/chrome/thank-you.html?statcb=0&installdataindex=empty&defaultbrowser=0" target="_blank">推荐使用Chrome浏览器</a>
      </div>
    </el-form>
  </div>
</template>

<script>
import {login} from '@/api/collect'

export default {
  name: 'Login',
  data() {
    const passwordRule = (rule, value, callback) => {
        const reg = /^(?=.*?[0-9])(?=.*?[a-zA-Z])[0-9a-zA-Z]{8,20}$/g
        if (!reg.test(value)) {
            callback(new Error('请输入8-20位数字加字母组合密码'))
        } else {
            callback()
        }
    }
    return {
      loginForm: {
        loginName: '',
        pwd: ''
      },
      loginRules: {
        loginName:[{required: true, trigger: 'blur', message: '用户名不能为空'}],
        pwd:[{required: true, trigger: 'blur', validator: passwordRule}]
      },
      loading: false,
      passwordType: 'password',
      redirect: undefined
    }
  },
  watch: {
    $route: {
      handler: function(route) {
        this.redirect = route.query && route.query.redirect
      },
      immediate: true
    }
  },
  methods: {
    showPwd() {
      if (this.passwordType === 'password') {
        this.passwordType = ''
      } else {
        this.passwordType = 'password'
      }
      this.$nextTick(() => {
        this.$refs.password.focus()
      })
    },
    handleLogin(ruleForm) {
      this.$refs[ruleForm].validate((typeValid) => {
        if(typeValid){
          let myData={
            loginName:this.loginForm.loginName,
            pwd:this.loginForm.pwd
          }
          login(myData)
          .then(res=>{
            if(res.code === 200){
              sessionStorage.setItem("user", JSON.stringify(res.result))
              // sessionStorage.setItem("userid",res.result.id)
              this.$router.push({
                path:'/home/basicInfo',
                quert:{
                  type:res.result.comType
                }
              })
            }else{
              this.$message.error(res.message)
            }
          })
        }else{
          return false
        }
      })
    },
    handleLoginDashbord(ruleForm) {
      this.$refs[ruleForm].validate((typeValid) => {
        if(typeValid){
          let myData={
            loginName:this.loginForm.loginName,
            pwd:this.loginForm.pwd
          }
          login(myData)
          .then(res=>{
            if(res.code === 200){
              sessionStorage.setItem("user", JSON.stringify(res.result))
              // sessionStorage.setItem("userid",res.result.id)
              this.$router.push({
                path:'/',
                quert:{
                  type:res.result.comType
                }
              })
            }else{
              this.$message.error(res.message)
            }
          })
        }else{
          return false
        }
      })
    },
    handleRegister(){
      this.$router.push({
        path:'/register'
      })
    },
    handleReset(){
      this.$router.push({
        path:'/reset'
      })
    },
    handleHelp(){
      this.$router.push({
        path:'/help'
      })
    },
    goResetPassword(){
      this.$router.push({
        path:'/repass'
      })
    }
  }
}
</script>

<style lang="scss">
/* 修复input 背景不协调 和光标变色 */
/* Detail see https://github.com/PanJiaChen/vue-element-admin/pull/927 */

$bg:#283443;
$light_gray:#fff;
$cursor: #fff;

@supports (-webkit-mask: none) and (not (cater-color: $cursor)) {
  .login-container .el-input input {
    color: $cursor;
  }
}

/* reset element-ui css */
.login-container {
  .el-input {
    display: inline-block;
    height: 47px;
    width: 85%;

    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      color: $light_gray;
      height: 47px;
      caret-color: $cursor;

      &:-webkit-autofill {
        box-shadow: 0 0 0px 1000px $bg inset !important;
        -webkit-text-fill-color: $cursor !important;
      }
    }
  }

  .el-form-item {
    border: 1px solid rgba(255, 255, 255, 0.1);
    background: rgba(0, 0, 0, 0.1);
    border-radius: 5px;
    color: #454545;
  }
  .forPwclass{
    border: none;
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 30px;
  }
}
</style>

<style lang="scss" scoped>
$bg:#2d3a4b;
$dark_gray:#889aa4;
$light_gray:#eee;

.login-container {
  min-height: 100%;
  width: 100%;
  background-color: $bg;
  overflow: hidden;

  .login-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    padding: 160px 35px 0;
    margin: 0 auto;
    overflow: hidden;
  }


  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }

  .title-container {
    position: relative;

    .title {
      font-size: 26px;
      color: $light_gray;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
  }

  .show-pwd {
    position: absolute;
    right: 10px;
    top: 7px;
    font-size: 16px;
    color: $dark_gray;
    cursor: pointer;
    user-select: none;
  }
  .regBox{
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .downloadClass{
    text-align: center;
    margin: 30px 0;
    color: #fff;
  }
  .tips{
    font-size: 14px;
    color: #889aa4;
    line-height: 24px;
  }
}
</style>
