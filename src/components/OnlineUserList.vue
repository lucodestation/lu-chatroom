<template>
  <div>
    <h3 align="center">在线用户</h3>
    <el-table
      :data="this.onlineUserList.map((item) => ({ username: item }))"
      :show-header="false"
      empty-text="暂无用户"
      border
      stripe
    >
      <el-table-column prop="username" align="center"> </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  name: 'OnlineUserList',
  props: ['onlineUserList'],
};
</script>
