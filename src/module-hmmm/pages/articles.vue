<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row>
        <el-button type="success">新增面试技巧</el-button>
      </el-row>
      <el-row>
        关键字
        <input type="text" placeholder="请输入题目编号/题干" style="font-size:12px; height:30px;margin-top:20px;">
        <div style="float:right">
          <el-button>清除</el-button>
          <el-button type="primary">搜索</el-button>
        </div>
      </el-row>
      
    </div>
  </div>
</template>

<script>
export default {
  name: 'ArticlesList',
  data() {
    return {}
  }
}
</script>

<style scoped>
</style>
