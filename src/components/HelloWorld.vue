<template>
  <div class="login-container">
    <div class="header">
      <img class="company-icon" src="~@/assets/imgs/companyicon.png" alt="">
      <span class="company-name">城云科技</span>
    </div>
    <div class="login-form-box">
      <el-form autoComplete="on" :model="loginForm" :rules="loginRules" ref="loginForm" label-position="left" label-width="0px"
        class="card-box login-form">
        <div class="title">
          <img class="app-logo" src="~@/assets/imgs/applogo.png" alt="">
          <span class="app-name">云脑智库</span>
        </div>
        <el-form-item prop="username" class="username">
          <div class="left-wrap"></div>
          <img class="icon" src="~@/assets/imgs/usericon.png" alt="">
          <el-input name="username" type="text" v-model="loginForm.username" autoComplete="on" placeholder="username" />
        </el-form-item>
        <el-form-item prop="password">
          <div class="left-wrap"></div>
          <img class="icon" src="~@/assets/imgs/passwordicon.png" alt="">
          <el-input name="password" :type="pwdType" @keyup.enter.native="handleLogin" v-model="loginForm.password" autoComplete="on"
            placeholder="password"></el-input>
            <span class="show-pwd" @click="showPwd"></span>
        </el-form-item>
        <el-form-item >
          <a class="forget-password" :to="{ path: '/forgetpwd/validateEmail'}">忘记密码？</a>
          <el-button type="primary" class="login-btn" style="width:100%;" :loading="loading" @click.native.prevent="handleLogin">
            登录
          </el-button>
        </el-form-item>
      </el-form>
    </div>
    <div class="rc-count-box" >
      <div class="item">
        <span class="title">
          <img class="icon" src="~@/assets/imgs/diamond.png" alt="">
          <span class="text">响应中心共享数</span>
        </span>
        <DigitRoll
          ref='digitroll'
          :rollDigits='shareFileAmount'
          :flipStra = "false"
          class="num-box"
        />
      </div>
      <div class="item">
        <span class="title">
          <img class="icon" src="~@/assets/imgs/diamond.png" alt="">
          <span class="text">下载总次数</span>
        </span>
        <DigitRoll
          ref='digitroll'
          :rollDigits='downloadFileAmount'
          :flipStra = "false"
          class="num-box"
        />
      </div>
      <div class="item">
        <span class="title">
          <img class="icon" src="~@/assets/imgs/diamond.png" alt="">
          <span class="text">入库项目数</span>
        </span>
        <DigitRoll
          ref='digitroll'
          :rollDigits='planCount'
          :flipStra = "false"
          class="num-box"
        />
      </div>
      <div class="item">
        <span class="title">
          <img class="icon" src="~@/assets/imgs/diamond.png" alt="">
          <span class="text">入库产品数</span>
        </span>
        <DigitRoll
          ref='digitroll'
          :rollDigits='reposCount'
          :flipStra = "false"
          class="num-box"
        />
      </div>
    </div>
  </div>
</template>

<script>
import DigitRoll from '@huoyu/vue-digitroll';
export default {
  name: 'login',
  data() {
    const validateUsername = (rule, value, callback) => {
      // if (!isvalidUsername(value)) {
      //   callback(new Error('请输入正确的用户名'))
      // } else {
      //   callback()
      // }
      if (value !== '') {
        callback()
      } else {
        callback(new Error('请输入正确的用户名'))
      }
    }
    const validatePass = (rule, value, callback) => {
      if (value.length < 2) {
        callback(new Error('密码不能小于2位'))
      } else {
        callback()
      }
    }
    return {
      shareFileAmount: 0,
      downloadFileAmount: 0,
      planCount: 0,
      reposCount: 0,
      timehHandler: null,
      timeSet: 5 * 60 * 1000,
      loginForm: {
        username: 'admin',
        password: 'sa',
        projectId: ''
      },
      loginRules: {
        username: [{ required: true, trigger: 'blur', validator: validateUsername }],
        password: [{ required: true, trigger: 'blur', validator: validatePass }]
      },
      loading: false,
      isLocalProject: false,
      pwdType: 'password',
      getDisplayDataUrl: '/v1/cm/common/displayData'
    }
  },
  computed: {
    targetPath() {
      return this.$route.query['targetPath']
    },
    searchContent() {
      return this.$route.query['searchContent']
    },
    tilePath() {
      return this.$route.query['tilePath']
    }
  },
  components: {
    DigitRoll
  },
  methods: {
    showPwd() {
      if (this.pwdType === 'password') {
        this.pwdType = ''
      } else {
        this.pwdType = 'password'
      }
    },
    handleLogin() {
      this.$refs.loginForm.validate(valid => {
        if (valid) {
          this.loading = true
          this.$store.dispatch('Login', this.loginForm).then(() => {
            this.loading = false
            if(this.targetPath) {
              this.$router.push( this.targetPath)
            }
            else if (this.searchContent){
              this.$router.push({path: '/searchRes', query: {
                searchContent: this.searchContent
              }})
            }else if( this.tilePath) {
              this.$router.push( this.tilePath)
            }
            else{
              this.$router.push({ path: '/' })
            }
          }).catch(() => {
            this.loading = false
          })
        } else {
          // console.log('error submit!!')
          return false
        }
      })
    },
    getDisplayData(){
      // const url = this.getDisplayDataUrl
      // request({
      //   url,
      //   method: 'post' 
      // }).then(( {data} ) => {
      //   this.shareFileAmount = data.shareFileAmount
      //   this.downloadFileAmount = data.shareFileAmount
      //   this.planCount = data.planCount
      //   this.reposCount = data.reposCount
      // }).catch((error) =>{
      //   this.$alert(error)
      // })
    }
  },
  mounted() {
    //  this.getDisplayData()
     this.timehHandler = setInterval(()=>{
       this.getDisplayData()
     }, this.timeSet)
  },
  destroyed() {
    // 清理定时器
    this.timehHandler && clearInterval(this.timehHandler)
  },
}
</script>

<style rel="stylesheet/scss" lang="scss">
  body{
    font-size: 14px;
  }
  $dark_gray:#889aa4;
  @font-face {
    font-family: 'digital';
    src: url("~@/fonts/LCDMonoNormal.ttf");
  }
  .login-container {
    position: fixed;
    top: 0;
    left: 0;
    height: 100%;
    width:100%;
    background: url(~@/assets/imgs/loginbg.png);
    background-size: 100%;
    display: flex;
    flex-direction: column;
    .header{
      width: 18.4625rem;
      height: 42px;
      margin: 0.75rem auto 0;
      .company-icon{
        width: 0.7rem;
        vertical-align: middle;
      }
      .company-name{
        color: rgba(255, 255, 255, 1);
        font-size: 0.3rem;
        font-family: PingFangSC-Regular;
        vertical-align: middle;
        margin-left: 0.45rem;
      }
    }
    input:-webkit-autofill {
      box-shadow: 0 0 0px 1000px #fff inset !important;
      -webkit-box-shadow: 0 0 0px 1000px #fff inset !important;
      -webkit-text-fill-color: #666 !important;
    }
    input {
      background: transparent;
      border: 0px;
      -webkit-appearance: none;
      border-radius: 0px;
      padding: 12px 5px 12px 15px;
      height: 0.65rem;
    }
    .el-input {
      display: inline-block;
      height: 0.65rem;
      width: 85%;
    }
    .title {
      text-align: center;
      margin-bottom: 1.4375rem;
      .app-logo{
        width: 0.9625rem;
        vertical-align: middle;
      }
      .app-name{
        color: rgba(255, 255, 255, 1);
        font-size: 0.725rem;
        font-family: PingFangSC-Medium;
        margin-left: 0.6325rem;
        vertical-align: middle
      }
    }
    .login-form-box{
      flex: 1;
      display: flex;
      align-items: center;
      justify-content: center;
      .login-form {
        width: 7.0125rem;
        .el-form-item {
          background: #fff;
          color: #000;
          height: 0.65rem;
          &:nth-child(4){
            background: none;
            .el-form-item__content{
              .forget-password{
                float: right;
                color: #F0E5D9;
                font-size: 0.2rem;
                margin-top: -0.2rem;
              }
            }
          }
          .el-form-item__content{
            position: relative;
            .login-btn{
              height: 0.65rem;
              background: linear-gradient(to right, #ED9239, #FF6400);
              border: 0;
              border-radius: 0;
              font-size: 0.25rem;
              &:hover{
                background: linear-gradient(to right, #E1760E, #C74E00);
              }
            }
            .left-wrap{
              position: absolute;
              top: 0;
              left: 0;
              height: 0;
              width: 1.6rem;
              border-top: 0.65rem solid #ED9239;
              border-right: 40px solid transparent;
              z-index: 10;
            }
            .icon{
              width: 0.3rem;
              position: absolute;
              border-style: none;
              left: 0.5rem;
              top: 0.15rem;
              z-index: 20;
            }
            .el-input{
              padding-left: 7rem;
              background-color: #fff;
            }
          }
        }
      }
    }
    .rc-count-box{
      width: 18.4625rem;
      height: 102px;
      margin: 0 auto 1rem;
      display: flex;
      justify-content: space-between;
      .item{
        .title{
          .icon{
            vertical-align: middle;
            width: 0.128125rem;
          }
          .text{
            vertical-align: middle;
            color: rgba(198, 98, 4, 1);
            font-size: 0.25rem;
            text-align: center;
            font-family: PingFangSC-Regular;
            margin-left: 0.1375rem;
          }
        }
        .num-box{
          color: #fff;
          font-size: 0.25rem;
          font-family: 'digital';
          margin-top: 0.3875rem;
          .d-roll-list{
            .d-roll-item{
              text-align: center;
              width: 0.2375rem;
              height: 0.5rem;
              flex-grow: 0;
              background-color: rgba(82, 34, 0, 1);
              box-shadow: inset 0px 0px 7px 0px rgba(224, 116, 11, 1);
              border: 1px solid rgba(217, 109, 2, 1);
              margin-left: 4px;
            }
          }
        }
      }
    }

    .show-pwd {
      position: absolute;
      top: 6px;
      right: 10px;
      font-size: 16px;
      color: $dark_gray;
      cursor: pointer;
      user-select:none;
      color: #FF6400;
    }
  }
</style>
