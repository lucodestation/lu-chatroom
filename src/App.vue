<template>
  <el-container id="app" class="container">
    <!-- 大标题 -->
    <el-header>
      <h1 v-html="pageTitle"></h1>
    </el-header>

    <el-container class="main">
      <!-- 侧栏。在线用户列表 -->
      <el-aside style="width: 240px;">
        <!-- 在线用户列表 -->
        <OnlineUserList :onlineUserList="onlineUserList"/>
      </el-aside>

      <el-main style="height: 100%;">
        <!-- 聊天界面 -->
        <Chat v-if="isLogged" :messageList="messageList" :currentUser="currentUser" @submitMessageEvent="submitMessageEvent" />
        
        <!-- 登录界面 -->
        <Login 
        v-else 
        @loginEvent="loginEvent"/>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>

import { io } from 'socket.io-client'

import OnlineUserList from './components/OnlineUserList'
import Chat from './components/Chat'
import Login from './components/Login'

// const socket = io();



export default {
  data() {
    return {
      pageTitle: '聊天室',
      socket: '',
      onlineUserList: [],
      currentUser: '',
      messageList: [],
      isLogged: false
    }
  },
  computed: {
  },
  components: {
    OnlineUserList,
    Chat,
    Login
  },
  methods: {
    loginEvent(username) {
      this.socket.emit("login", username);
      this.currentUser = username;
    },
    submitMessageEvent(message) {
      this.socket.emit("message", message);
    }
    
  },
  mounted() {
    // 加载 loading
    const loading = this.$loading({
      lock: true,
      text: '正在连接服务器...',
      spinner: 'el-icon-loading',
      background: 'rgba(255, 255, 255, 0.7)'
    });

    // 连接 socket.io 服务器
    this.socket = io('https://lucodestation-chartoom-api.glitch.me');
    // 连接失败
    this.socket.io.on("error", error => {
      // 如果连接失败，禁止再次尝试连接
      // 否则在控制台会看到一直在请求连接
      this.socket.close();
      // 关闭加载 loading
      loading.close()
      // 消息提示
      this.$message.error({
        message: '连接 socket.io 服务器失败，请刷新后重试。',
        center: true
      });
    });
    // 连接成功
    this.socket.on('connect', () => {
      // 关闭加载 loading
      loading.close()
      this.$message.success({
        message: '连接 socket.io 服务器成功，请登录。',
        center: true
      });
    })

    // 监听服务器返回的在线用户列表
    // 这里的监听不能写到事件处理程序中，否则会触发多次
    this.socket.on("onlineUserList", result => {
      this.onlineUserList = [];
      this.onlineUserList = result;
    })

    // 监听登录状态，看是否登录成功
    this.socket.on("login", result => {
      if (result.code < 0) {
        this.$message.warning({
          message: result.message,
          center: true
        });
      } else {
        this.pageTitle = `欢迎 ${this.currentUser} 来到聊天室`;
        this.isLogged = true;
      }
    })

    // 监听服务器返回的（聊天）消息
    this.socket.on("returnMessage", data => this.messageList.push(data));
  }
}
</script>

<style>
html, body {
  height: 100%;
}
body {
  margin: 0;
}
h2 {
  text-align: center;
}
</style>
<style scoped>
.el-header {
  background-color: #409eff;
  color: #fff;
  text-align: center;
  height: auto !important;
}
.container {
  height: 100%;
}
.main {
  overflow: hidden;
}
</style>