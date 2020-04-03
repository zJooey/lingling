<template>
  <div class="login-wrap">
    <el-form ref="loginForm" :model="loginForm" status-icon :rules="rules" class="login-form">
      <el-form-item prop="account">
        <span class="svg-container">
          <svg-icon icon-class="user" />
        </span>
        <el-input
          v-model="loginForm.account"
          type="text"
          placeholder="请输入用户名"
          name="username"
          tabindex="1"
          autocomplete="off"
        >
          <i slot="prefix" class="el-icon-user" />
        </el-input>
      </el-form-item>
      <el-form-item prop="pass">
        <span class="svg-container">
          <svg-icon icon-class="password" />
        </span>
        <el-input
          v-model="loginForm.pass"
          type="password"
          placeholder="请输入密码"
          name="username"
          tabindex="2"
          autocomplete="off"
          @keyup.enter.native="submitForm('loginForm')"
        >
          <i slot="prefix" class="el-icon-lock" />
        </el-input>
      </el-form-item>
      <el-form-item>
        <el-button :loading="loading" type="primary" @click="submitForm('loginForm')">登录</el-button>
        <!--<el-button @click="resetForm('loginForm')">重置</el-button>-->
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    const validateAccount = (rule, value, callback) => {
      if (!value) {
        return callback(new Error('年龄不能为空'))
      }
    }
    const validatePass = (rule, value, callback) => {
      if (value.trim().length < 6) {
        callback(new Error('密码长度不能低于6位'))
      } else {
        callback()
      }
    }
    return {
      loginForm: {
        account: '',
        pass: ''
      },
      rules: {
        account: [{ required: true, validator: validateAccount, trigger: 'blur' }],
        pass: [{ required: true, validator: validatePass, trigger: 'blur' }]
      },
      loading: false
    }
  },
  methods: {
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.loading = true
        } else {
          console.log('error submit!!')
          return false
        }
      })
    },
    resetForm(formName) {
      this.$refs[formName].resetFields()
    }
  }
}
</script>

<style lang="scss" scoped>
  $dark_gray: #889aa4;
  .login-wrap {
    min-height: 100%;
    width: 100%;
    background: linear-gradient(rgba(196, 102, 0, 0.2), rgba(155, 89, 182, 0.2)), url('../../assets/login/gs.png');
    overflow: hidden;
    .el-input {
      display: inline-block;
      height: 47px;
      width: 85%;
    }
  }
  .login-form {
    position: relative;
    width: 520px;
    max-width: 100%;
    margin: 0 auto;
    padding: 160px 35px 0;
    text-align: center;
    overflow: hidden;
  }
  .svg-container {
    padding: 6px 5px 6px 15px;
    color: $dark_gray;
    vertical-align: middle;
    width: 30px;
    display: inline-block;
  }
</style>
