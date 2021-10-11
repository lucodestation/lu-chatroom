<template>
  <el-container>
    <el-header>
      <h2 class="flow-text">欢迎 <strong v-html="currentUser"></strong> 进入聊天室</h2>
    </el-header>
    <el-main ref="showMessageList">
      <div v-for="(item, index) in messageList" :key="index">
        <!-- 当前用户聊天信息 -->
        <dl v-if="currentUser === item.username" class="i-said">
          <dt><span class="date">({{item.date}})</span> {{item.username}}</dt>
          <dd>{{item.message}}</dd>
        </dl>
        <!-- 其它用户聊天信息 -->
        <dl v-else>
          <dt>{{item.username}} <span class="date">({{item.date}})</span></dt>
          <dd>{{item.message}}</dd>
        </dl>
      </div>
    </el-main>
    <el-footer style="height: auto;">
      <el-form>
        <el-form-item>
          <el-input
            ref="inputMessage"
            type="textarea"
            :rows="3"
            placeholder="请输入内容"
            v-model="message"
            @keydown.enter.native="keydown"
            >
          </el-input>
        </el-form-item>
        <el-form-item style="text-align: right;">
          <div class="top">
            <el-tooltip class="item" effect="light" content="Ctrl + Enter" placement="top">
              <el-button type="primary" @click="submitMessage">发送<i class="el-icon-s-promotion el-icon--right"></i></el-button>
            </el-tooltip>
          </div>
        </el-form-item>
      </el-form>
    </el-footer>
  </el-container>
</template>

<script>
export default {
  name: 'Chat',
  props: ['messageList', 'currentUser'],
  data() {
    return {
      message: '',
    }
  },
  watch: {
    messageList() {
      // 滚动滚动条
      setTimeout(() => {
        this.$refs.showMessageList.$el.scrollTop = this.$refs.showMessageList.$el.scrollHeight
      }, 0);
    }
  },
  methods: {
    submitMessage() {
      // 不能在 textarea 上使用 trim ，否则按 Enter 无法换行
      this.message = this.message.trim()
      if (!this.message) { return; }
      this.$emit('submitMessageEvent', this.message)
      this.message = ''
      this.$refs.inputMessage.focus()
    },
    keydown(event) {
      // Ctrl + Enter 发送消息
      event.ctrlKey && this.submitMessage()
    },
    currentTime() {
      const now = new Date();
      return `${now.getFullYear()}-${now.getMonth() + 1}-${now.getDate()} ${now.getHours()}:${now.getMinutes()}:${now.getSeconds()}`
    }
  },
  mounted() {
    this.$refs.inputMessage.focus()
  }
}
</script>

<style scoped>
.el-container {
  height: 100%;
}
.el-header {
  background: #d9ecff;
  padding: 0;
}
h2 {
  font-weight: normal;
  white-space: nowrap;
  line-height: 1em;
  /* background: red; */
}
.el-main {
  border: 1px solid #a0cfff;
}
.el-footer {
  padding: 0;
}
.i-said {
  color: #409eff;
  text-align: right;
}
dl dt {
  color: #888;
  margin: 10px 0;
  white-space: nowrap;
}
dl dd {
  display: inline-block;
  white-space: pre;
  vertical-align: top;
  margin: 0;
  padding: 0 1em;
}
dl dt .date {
  font-size: 14px;
  color: #aaa;
}

</style>