<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-button type="primary">新增</el-button>
      <el-form ref="searchFormRef" :model="searchForm">
        <el-row>
          <el-col :span="6">
            学科
            <el-select v-model="searchForm.subjectID" placeholder="请选择" style="width:130px">
              <el-option
                v-for="item in subjectIDList"
                :label="item.label"
                :key="item.value"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            难度
            <el-select v-model="searchForm.difficulty" placeholder="请选择" style="width:130px">
              <el-option
                v-for="item in difficultyList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            试题类型
            <el-select v-model="searchForm.questionType" placeholder="请选择" style="width:130px">
              <el-option
                v-for="item in questionTypeList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            标签
            <el-select v-model="searchForm.tags" placeholder="请选择" style="width:130px">
              <el-option
                v-for="item in tagsList"
                :key="item.id"
                :label="item.tagName"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="7">
            城市
            <el-select
              @change="getCitys"
              style="width:120px;"
              v-model="searchForm.province"
              placeholder="请选择省份"
            >
              <el-option v-for="item in provincesList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
            <el-select style="width:120px;" v-model="searchForm.city" placeholder="请选择城市">
              <el-option v-for="item in cityList" :label="item" :value="item" :key="item"></el-option>
            </el-select>
          </el-col>
          <el-col :span="6">
            题目备注
            <el-input v-model="searchForm.remarks" placeholder="题目备注" style="width:130px;"></el-input>
          </el-col>
          <el-col :span="6">
            企业简称
            <el-input v-model="searchForm.shortName" placeholder="企业简称" style="width:130px;"></el-input>
          </el-col>
          <el-col :span="7">
            方向
            <el-select v-model="searchForm.direction" placeholder="请选择" style="width:130px;">
              <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-col>
        </el-row>
        <el-row :gutter="10">
          <el-col :span="7">
            录入人
            <el-select v-model="searchForm.creatorID" placeholder="请选择" style="width:130px;">
              <el-option
                v-for="item in creatorIDList"
                :key="item.id"
                :label="item.username"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-col>
          <el-col :span="7">
            关键字
            <el-input type="text" placeholder="请输入关键字" v-model="searchForm.keyword" style="width:150px;"></el-input>
          </el-col>
          <el-col :span="7">
            二级目录
            <el-select v-model="searchForm.catalogIDL" placeholder="请输入二级目录" style="width:130px;">
              <el-option
                v-for="item in catalogIDList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-col>
        </el-row>
        <el-row style="text-align:right;margin-bottom:20px;">
           <el-button>清除</el-button>
            <el-button type="primary" @click="getQuestionsList()">搜索</el-button>
        </el-row>
      </el-form>
      <el-table :data="questionsList" style="width:100%">
        <el-table-column label="序号" type="index"></el-table-column>
        <el-table-column label="试题编号" prop="number"></el-table-column>
        <el-table-column label="学科" prop="subjectID"></el-table-column>
        <el-table-column label="题型" :formatter="questionTypeFMT"  prop="questionType"></el-table-column>
        <el-table-column label="题干" prop="question"></el-table-column>
        <el-table-column label="录入时间" prop="addDate">
           <template slot-scope="stData">
              <span style="margin-left: 10px">{{ stData.row.addDate | parseTimeByString }}</span>
           </template>
        </el-table-column>
        <el-table-column label="难度" :formatter="difficultyFMT" prop="difficulty"></el-table-column>
        <el-table-column label="录入人" prop="creator"></el-table-column>
        <el-table-column label="操作" width="200">
          <template slot-scope="stData">
            <a href="#">预览</a>
            <a href="#">修改</a>
            <a href="#" @click.prevent="delList(stData.row)">删除</a>
            <a href="#">加入精选</a>
          </template>
        </el-table-column>
      </el-table>
   <el-row type="flex" justify="center">
        <el-pagination :current-page="searchForm.page" :page-size="searchForm.pagesize" background layout="prev, pager, next" :total="searchForm.total">
        </el-pagination>
   </el-row>
    </div>
  </div>
</template>

<script>
import { simple as subjectSimple } from '@/api/hmmm/subjects' // 学科模块
import {
  difficulty as difficultyList,
  questionType as questionTypeList,
  direction as directionList
} from '@/api/hmmm/constants' // 难度模块
import { list as tagsList } from '@/api/hmmm/tags' // 标签模块
import { provinces, citys } from '@/api/hmmm/citys' // 城市模块
import { simple as usersSimple } from '@/api/base/users' // 录入人
import { simple as directorySimple } from '@/api/hmmm/directorys' // 二级目录
import { list as questionsList, remove as questionsRemove } from '@/api/hmmm/questions'

export default {
  name: 'QuestionsList',
  methods: {
    // 获取学科接口
    async getSubjectIDList() {
      var res = await subjectSimple()
      console.log(res)
      this.subjectIDList = res.data
    },
    // 获取标签接口
    async getTagsList() {
      var res = await tagsList()
      console.log(res)
      this.tagsList = res.data.items
    },
    // 获取城市
    getCitys(pname) {
      this.searchForm.city = ''
      this.cityList = citys(pname)
    },
    // 录入人
    async getcreatorIDList() {
      // console.log(usersSimple())
      var res = await usersSimple()
      console.log(res)
      this.creatorIDList = res.data
    },
    // 二级目录
    async getCatalogIDList() {
      var res = await directorySimple()
      console.log(res)
      this.catalogIDList = res.data
    },
    // 获得基础题库列表
    async getQuestionsList() {
      // alert(1)
      var res = await questionsList(this.searchForm)
      console.log(res)
      this.searchForm.total = res.data.pages
      this.questionsList = res.data.items
    },
    // 题型数字转汉字
    questionTypeFMT(row, column, cellValue) {
      // console.log(cellValue)
      return this.questionTypeList[cellValue - 1]['label']
    },
    // 难度数字转汉字
    difficultyFMT(row, column, cellValue) {
        return this.difficultyList[cellValue - 1]['label']
    },
    // 删除
    delList(item) {
      this.$confirm('你确定要删除吗？').then(() => {
        questionsRemove(item)
        this.getQuestionsList()
      })
      
    }

  },
  data() {
    return {
      // 基础搜索数据列表部分
      subjectIDList: [], // 学科
      difficultyList, // 难度
      questionTypeList, // 试题类型
      tagsList: [], // 标签列表
      provincesList: provinces(), // 省份
      cityList: [], // 城市
      directionList, // 方向
      creatorIDList: [], // 录人人
      catalogIDList: [], // 二级目录成员
      questionsList: [], // 基础题库列表信息

      searchForm: {
        // 搜索表单数据对象
        subjectID: '', // 学科
        difficulty: '', // 难度
        questionType: '', // 试题类型
        tags: '', // 标签
        province: '', // 省份
        city: '', // 城市
        remarks: '', // 题目备注
        shortName: '', // 企业检查
        direction: '', // 方向
        creatorID: '', // 录入人
        catalogIDL: '', // 二级目录
        keyword: '', // 关键字
        page: 1, // 当前页数
        pagesize: 10, // 每页条数
        total: 1
      }
    }
  },

  created() {
    // 获得学科信息
    this.getSubjectIDList()
    // 获得标签信息
    this.getTagsList()
    // 获得二级目录信息
    this.getCatalogIDList()
    // 获得录入人信息
    this.getcreatorIDList()
    // 获取基础题库信息
    this.getQuestionsList()
  }
}
</script>

<style scoped>
.el-row {
  margin-top: 15px;
}
.el-col-6 {
  width: 20%;
}
</style>
