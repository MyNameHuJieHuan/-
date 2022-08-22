<template>
  <el-dialog
    :title="!dataForm.debitId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="读者" prop="userId">
      <el-select v-model="value" filterable placeholder="请选择">
        <el-option
          v-for="item in options"
          :key="item.readerId"
          :label="item.readerName"
          :value="item.readerId">
        </el-option>
      </el-select>
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
        options: [],
        value: '',
        userName: '',
        visible: false,
        dataForm: {
          debitId: 0,
          userId: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '读者不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    created: function () {
      this.getReaderList()
    },
    methods: {
      // 创建方法用来查询所有的读者信息
      getReaderList () {
        this.$http({
          url: this.$http.adornUrl('sys/user/getReaderList'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.options = data.list
        })
      },
      init (id) {
        this.dataForm.debitId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.debitId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/debitinfo/info/${this.dataForm.debitId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.value = data.debitInfo.userId
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
              url: this.$http.adornUrl(`/bookmanager/debitinfo/${!this.dataForm.debitId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'debitId': this.dataForm.debitId || undefined,
                'userId': this.value,
                'insertName': this.userName,
                'updateName': this.userName
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
              // 清空表单中的数据
              this.value = ''
            })
          }
        })
      }
    }
  }
</script>
