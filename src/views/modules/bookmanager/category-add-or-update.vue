<template>
  <el-dialog
    :title="!dataForm.categoryId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    
    <el-form-item label="类别编码" prop="categoryCode">
      <el-input v-model="categoryCode" placeholder="请输入类别编码"></el-input>
    </el-form-item>
    <el-form-item label="类别名" prop="categoryName">
      <el-input v-model="categoryName" placeholder="请输入类别名"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        categoryName: '',
        categoryCode: '',
        dataForm: {
          categoryId: ''
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.categoryId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.categoryId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/category/info/${this.dataForm.categoryId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.categoryCode = data.category.categoryCode
                this.categoryName = data.category.categoryName
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/category/${!this.dataForm.categoryId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'categoryId': this.dataForm.categoryId || undefined,
                'categoryName': this.categoryName,
                'categoryCode': this.categoryCode
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              // 清空数据
                this.categoryName = ''
                this.categoryCode = ''
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
