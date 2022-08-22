<template>
  <el-dialog
    :title="!dataForm.libraryId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="图书馆名称" prop="libraryName">
      <el-input v-model="dataForm.libraryName" placeholder="图书馆名称"></el-input>
    </el-form-item>
    <el-form-item label="图书馆地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="图书馆地址"></el-input>
    </el-form-item>
    <el-form-item label="图书馆编码" prop="libraryCode">
      <el-input v-model="dataForm.libraryCode" placeholder="图书馆编码"></el-input>
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
        userName: '',
        dataForm: {
          libraryId: 0,
          libraryName: '',
          address: '',
          libraryCode: '',
          deleted: '',
          insertTime: '',
          insertName: '',
          updateName: '',
          updateTime: ''
        },
        dataRule: {
          libraryName: [
            { required: true, message: '图书馆名称不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '图书馆地址不能为空', trigger: 'blur' }
          ],
          libraryCode: [
            { required: true, message: '图书馆编码不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.libraryId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.libraryId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/libraryinfo/info/${this.dataForm.libraryId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.libraryName = data.libraryInfo.libraryName
                this.dataForm.address = data.libraryInfo.address
                this.dataForm.libraryCode = data.libraryInfo.libraryCode
                this.dataForm.deleted = data.libraryInfo.deleted
                this.dataForm.insertTime = data.libraryInfo.insertTime
                this.dataForm.insertName = data.libraryInfo.insertName
                this.dataForm.updateName = data.libraryInfo.updateName
                this.dataForm.updateTime = data.libraryInfo.updateTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.userName = this.$cookie.get('userName')
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/libraryinfo/${!this.dataForm.libraryId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'libraryId': this.dataForm.libraryId || undefined,
                'libraryName': this.dataForm.libraryName,
                'address': this.dataForm.address,
                'libraryCode': this.dataForm.libraryCode,
                'insertName': this.userName
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
