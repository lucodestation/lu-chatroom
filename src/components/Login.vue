<template>
  <div>
      <h2>登录</h2>
        <el-form @submit.native="submit">
          <el-col :xs="{span: 18, offset: 3}" :span="12" :offset="6">
            <el-col :span="20">
              <el-input v-model.trim="username" placeholder="随便起个用户名称" ref="loginInput" maxlength="8" show-word-limit ></el-input>
            </el-col>
            <el-col :span="4">
              <el-button type="primary" native-type="submit">
                登录
                <i v-if="loginStart" class="el-icon-loading el-icon--right"></i>
              </el-button>
            </el-col>
          </el-col>
        </el-form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  props: ['loginEvent', 'loginStart'],
  data() {
    return {
      username: ''
    }
  },
  methods: {
    submit(event) {
      event.preventDefault();
      if (!this.username) {
        this.$message.warning('请输入一个用户名。');
        return;
      }
      this.$emit('loginEvent', this.username)
      
    }
  }
}
</script>