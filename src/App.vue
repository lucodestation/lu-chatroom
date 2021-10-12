<template>
  <el-container id="app" class="container">
    <!-- 大标题 -->
    <el-header>
      <el-row align="middle" type="flex">
        <el-col :span="1">
          <i class="el-icon-user" @click="drawer = true"></i>
        </el-col>
        <el-col :span="22">
          <h1 class="flow-text">在线聊天室</h1>
        </el-col>
        <el-col :span="1">
          <i class="el-icon-info" @click="dialogVisible = true"></i>
        </el-col>
      </el-row>
    </el-header>

    <el-container class="main">
      <!-- 侧栏。在线用户列表 -->
      <el-aside style="width: 160px;">
        <!-- 在线用户列表 -->
        <OnlineUserList :onlineUserList="onlineUserList"/>
      </el-aside>

      <el-main style="height: 100%;">
        <!-- 聊天界面 -->
        <Chat v-if="isLogged" :messageList="messageList" :currentUser="currentUser" @submitMessageEvent="submitMessageEvent" />
        
        <!-- 登录界面 -->
        <Login 
        v-else 
        :loginStart="loginStart"
        @loginEvent="loginEvent"/>
      </el-main>
    </el-container>

    <el-drawer
      title="在线用户"
      :with-header="false"
      :visible.sync="drawer"
      :size="'44%'"
      :direction="direction">
      <OnlineUserList :onlineUserList="onlineUserList"/>
    </el-drawer>

    <el-dialog
      title=""
      :visible.sync="dialogVisible"
      width="80%"
      >

      <div>
        <h3>使用方法：</h3>
        <ul>
          <li>随便输入一个用户名（不能和已登录的用户名相同）即可登录。</li>
          <li>登录后即可开始聊天。输入聊天内容后点击 <strong>发送</strong> 或按 <strong><kbd>Ctrl</kbd> + <kbd>Enter</kbd></strong> 即可发送消息。</li>
          <li>快让您的朋友们加入聊天室大家一块儿畅聊吧！</li>
        </ul>

        <h3>注意事项：</h3>
        <ul>
          <li>您发送的消息对当前在线的所有用户可见，请注意个人隐私。</li>
          <li>您登录的用户会在浏览器刷新后自动下线，并且所有聊天记录不会被保存。</li>
        </ul>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogVisible = false">我已了解</el-button>
      </span>
    </el-dialog>
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
      socket: '',
      onlineUserList: [],
      currentUser: '',
      messageList: [],
      isLogged: false,
      loginStart: false,
      isOnline: false,
      drawer: false,
      direction: 'ltr',
      dialogVisible: false
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
    loginEvent: async function (username) {
      this.loginStart = true;
      this.socket.emit("login", username);
      this.currentUser = username;
    },
    submitMessageEvent(message) {
      this.socket.emit("message", message);
    }
    
  },
  mounted() {

    // 连接 socket.io 服务器
    this.socket = io('https://lucodestation-chartoom-api.glitch.me');
    // 连接失败
    this.socket.io.on("error", error => {
      // 如果连接失败，禁止再次尝试连接
      // 否则在控制台会看到一直在请求连接
      this.socket.close();

      if (this.loginStart) {
        this.$message.error({
          message: '服务器连接失败，请稍后刷新重试或联系管理员。',
          center: true
        });
        this.loginStart = false
      }
    });
    // 连接成功
    this.socket.on('connect', () => {
      console.log('服务器连接成功');
      this.isOnline = true;
    });

    // 监听服务器返回的在线用户列表
    // 这里的监听不能写到事件处理程序中，否则会触发多次
    this.socket.on("onlineUserList", result => {
      this.onlineUserList = [];
      this.onlineUserList = result;
    })

    // 监听登录状态
    this.socket.on("login", result => {
      if (result.code < 0) {
        // 登录失败
        this.$message.warning({
          message: result.message,
          center: true
        });
        this.loginStart = false;
      } else {
        // 登录成功
        this.isLogged = true;
        this.dialogVisible = true;
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
  color: #444;
}
h2 {
  text-align: center;
}
.el-icon-user {
  visibility: hidden;
}
@media (min-width: 900px) {
  .el-dialog {
    width: 50% !important;
  }
}
@media (max-width: 600px) {
  .el-aside {
    display: none;
  }
  .el-icon-user {
    visibility: visible;
  }
}
@media (max-width: 320px) {
  .el-main.el-main {
    padding: 20px 4px;
  }
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