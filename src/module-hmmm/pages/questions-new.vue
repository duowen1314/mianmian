<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form ref="addFormRef" :model="addForm">
        <el-form-item label="学科：">
          <el-select v-model="addForm.subjectID" placeholder="请选择">
            <el-option v-for="item in subjectIDList" :key="item.value" :label="item.label" :value="item.value"></el-option>
          </el-select>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import {simple as catalogIDList} from '@/api/hmmm/directorys'
import {simple as subjectIDList} from '@/api/hmmm/subjects'
export default {
  name: 'QuestionsNew',
  created() {
    this.getSubjectIDList()
    this.getCatalogIDList()
  },
  methods: {
    // 获取学科管理
   async getSubjectIDList() {
      var res = await subjectIDList()
      console.log(res)
      this.subjectIDList = res.data
    },
   async getCatalogIDList() {
     var res = await catalogIDList()
     console.log(res)
    }
  },
  
  data() {
    return {
      // 把各个要使用的数据列表信息声明好
      subjectIDList: [], // 学科
      catalogIDList: [], // 目录
      addForm: {
        subjectID: '',
        catalogID: '',
        enterpriseID: '',
        city: '',
        province: '',
        direction: '',
        questionType: '',
        difficulty: '',
        question: '',
        answer: '',
        remarks: '',
        videoURL: '',
        options: [
          {
          code: 'A',
          title: '',
          img: '',
          isRight: false
          },
          {
          code: 'B',
          title: '',
          img: '',
          isRight: true
          },
          {
          code: 'C',
          title: '',
          img: '',
          isRight: false
          },
          {
          code: 'D',
          title: '',
          img: '',
          isRight: true
          }
        ]
      }
    }
  }
}
</script>

<style scoped>
</style>
