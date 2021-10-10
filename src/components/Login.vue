<template>
  <div>
      <h2>登录</h2>
        <el-form @submit.native="submit">
          <el-col :span="12" :offset="6">
            <el-col :span="20">
              <el-input v-model.trim="username" placeholder="请输入用户名" ref="loginInput"></el-input>
            </el-col>
            <el-col :span="4">
              <el-button type="primary" native-type="submit">登录</el-button>
            </el-col>
          </el-col>
        </el-form>
  </div>
</template>

<script>
export default {
  name: 'Login',
  props: ['loginEvent', 'connected'],
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
  },
  watch: {
    connected() {
      this.connected && this.$refs.loginInput.focus()
    }
  }
}
</script>