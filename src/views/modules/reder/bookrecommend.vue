<template>
  <div class="recommend">
    <div style="margin-top: 15px;" class="top">
      <el-input placeholder="请输入内容" v-model="input3" class="input-with-select">
        <el-select v-model="select" slot="prepend" placeholder="请选择">
          <el-option label="书名" value="1"></el-option>
          <el-option label="作者" value="2"></el-option>
          <el-option label="出版社" value="3"></el-option>
        </el-select>
        <el-button slot="append" icon="el-icon-search"></el-button>
      </el-input>
      <el-button type="primary" icon="el-icon-s-data">书架</el-button>
    </div>
    
    <div>
      <div class="line"></div>
      <el-menu
        :default-active="activeIndex2"
        class="el-menu-demo"
        mode="horizontal"
        @select="handleSelect"
        background-color="#545c64"
        text-color="#fff"
        active-text-color="#ffd04b">
        <el-menu-item index="1">
          <span>推荐</span>
          <div class="demo-image">
            <div class="block" v-for="fit in fits" :key="fit">
              <!-- <el-image
                style="width: 100px; height: 100px"
                :src="url"
                :fit="fit"></el-image> -->
              <span class="demonstration">{{ fit }}</span>
            </div>
          </div>
        </el-menu-item>
        <el-menu-item index="2">哲学思想</el-menu-item>
        <el-menu-item index="3">科学技术</el-menu-item>
        <el-menu-item index="4">历史杂谈</el-menu-item>
        <el-menu-item index="5">经典名著</el-menu-item>
        <el-menu-item index="6">
          <el-dropdown :hide-on-click="false">
            <span class="el-dropdown-link">
              更多<i class="el-icon-arrow-down el-icon--right"></i>
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>黄金糕</el-dropdown-item>
              <el-dropdown-item>狮子头</el-dropdown-item>
              <el-dropdown-item>螺蛳粉</el-dropdown-item>
              <el-dropdown-item disabled>双皮奶</el-dropdown-item>
              <el-dropdown-item divided>蚵仔煎</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </el-menu-item>
      </el-menu>
    </div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        fits: ['file', 'file2'],
        url: 'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
        input3: '',
        select: '书名',
        activeIndex2: '1'
      }
    },
    methods: {
      handleSelect (key, keyPath) {
        console.log(key, keyPath)
      }
    }
  }
</script>
<style>
  .el-select .el-input {
    width: 130px;
  }
  .top{
    display: flex;
    justify-content: center;
    height: 60px;
    width: 900px;
    margin-left: 250px;
    margin-bottom:20px;
  }
  .el-input--medium .el-input__inner {
    height: 60px;
    line-height: 36px;
  }
  .el-input-group {
    line-height: normal;
    display: inline-table;
    width: 100%;
    border-collapse: separate;
    margin-right: 30px;
  }
  .el-dropdown-link {
    cursor: pointer;
    color: #f3f7fa;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }
</style>