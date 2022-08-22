<template>
  <el-dialog
    :title="!dataForm.circulationId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="借书时间" prop="borrowTime">
      <el-date-picker
        v-model="borrowTime"
        value-format="yyyy-MM-dd HH:mm:ss"
        type="date"
        placeholder="选择借书时间">
      </el-date-picker>
    </el-form-item>
    <el-form-item label="还书时间" prop="returnTime">
      <el-date-picker
        v-model="returnTime"
        value-format="yyyy-MM-dd HH:mm:ss"
        type="date"
        placeholder="选择还书时间">
      </el-date-picker>
    </el-form-item>
    <el-form-item label="书籍方式" prop="status">
      <el-select v-model="value" placeholder="请选择">
        <el-option
          v-for="item in option1"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="读者" prop="userId">
      <el-select v-model="value1" filterable placeholder="请选择">
        <el-option
          v-for="item in options"
          :key="item.readerId"
          :label="item.readerName"
          :value="item.readerId">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="书籍" prop="bookId">
      <el-select v-model="value2" filterable placeholder="请选择">
        <el-option
          v-for="item in option2"
          :key="item.bookId"
          :label="item.bookName"
          :value="item.bookId">
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
        borrowTime: '',
        returnTime: '',
        userName: '',
        visible: false,
        option2: [],
        options: [],
        dataForm: {
          circulationId: 0,
          userId: '',
          bookId: ''
        },
        option1: [{
          value: '1',
          label: '借书'
        }, {
          value: '2',
          label: '还书'
        }],
        value: '',
        value1: '',
        value2: '',
        dataRule: {
          value1: [
            { required: true, message: '读者ID不能为空', trigger: 'blur' }
          ],
          value2: [
            { required: true, message: '书籍ID不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    created: function () {
      this.getReaderList()
      this.getBooksList()
    },
    methods: {
      // 获取读者的相关信息
      getReaderList () {
        this.$http({
          url: this.$http.adornUrl('sys/user/getReaderList'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.options = data.list
        })
      },
      // 获取图书的相关信息
      getBooksList () {
        this.$http({
          url: this.$http.adornUrl('bookmanager/bookinfo/getBooksList'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.option2 = data.list
        })
      },
      init (id) {
        this.dataForm.circulationId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.circulationId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/circulationinfo/info/${this.dataForm.circulationId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              console.log(data)
              if (data && data.code === 0) {
                this.dataForm.borrowTime = data.circulationInfo.borrowTime
                this.dataForm.returnTime = data.circulationInfo.returnTime
                this.value = data.circulationInfo.status
                this.value1 = data.circulationInfo.userId
                this.value2 = data.circulationInfo.bookId
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
            console.log(this.borrowTime)
            console.log(this.returnTime)
            console.log(this.value)
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/circulationinfo/${!this.dataForm.circulationId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'circulationId': this.dataForm.circulationId || undefined,
                'borrowTime': this.borrowTime,
                'returnTime': this.returnTime,
                'status': this.value,
                'userId': this.value1,
                'bookId': this.value2,
                'insertName': this.userName,
                'updateName': this.userName
              })
            }).then(({data}) => {
              console.log(data)
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
              // 清空表单
              this.borrowTime = ''
              this.returnTime = ''
              this.value = ''
              this.option2 = []
              this.options = []
            })
          }
        })
      }
    }
  }
</script>
