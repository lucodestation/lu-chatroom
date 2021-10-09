<template>
  <div>
    <h2>在线用户</h2>
    <el-table
    :data="this.onlineUserList.map(item => ({ username: item }))"
    :show-header="false"
    border
    stripe>
      <el-table-column
        prop="username"
        align="center">
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: 'OnlineUserList',
  props: ['onlineUserList']
}
</script>
