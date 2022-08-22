<template>
  <el-dialog
    :title="!dataForm.recommendId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="书籍ID" prop="bookId">
      <el-input v-model="dataForm.bookId" placeholder="书籍ID"></el-input>
    </el-form-item>
    <el-form-item label="读者ID" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="读者ID"></el-input>
    </el-form-item>
    <el-form-item label="读者对该书籍的相关强度" prop="correlationintensity">
      <el-input v-model="dataForm.correlationintensity" placeholder="读者对该书籍的相关强度"></el-input>
    </el-form-item>
    <el-form-item label="是否已经加入书架收藏（0表示未收藏，1表示已收藏）" prop="status">
      <el-input v-model="dataForm.status" placeholder="是否已经加入书架收藏（0表示未收藏，1表示已收藏）"></el-input>
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
        dataForm: {
          recommendId: 0,
          bookId: '',
          userId: '',
          correlationintensity: '',
          status: ''
        },
        dataRule: {
          bookId: [
            { required: true, message: '书籍ID不能为空', trigger: 'blur' }
          ],
          userId: [
            { required: true, message: '读者ID不能为空', trigger: 'blur' }
          ],
          correlationintensity: [
            { required: true, message: '读者对该书籍的相关强度不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '是否已经加入书架收藏（0表示未收藏，1表示已收藏）不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.recommendId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.recommendId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/bookrecommend/info/${this.dataForm.recommendId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.bookId = data.bookRecommend.bookId
                this.dataForm.userId = data.bookRecommend.userId
                this.dataForm.correlationintensity = data.bookRecommend.correlationintensity
                this.dataForm.status = data.bookRecommend.status
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
              url: this.$http.adornUrl(`/bookmanager/bookrecommend/${!this.dataForm.recommendId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'recommendId': this.dataForm.recommendId || undefined,
                'bookId': this.dataForm.bookId,
                'userId': this.dataForm.userId,
                'correlationintensity': this.dataForm.correlationintensity,
                'status': this.dataForm.status
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
