<template>
  <el-dialog
    :title="!dataForm.forwardId ? '新增' : '修改'"
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
    <el-form-item label="图书馆" prop="libraryId">
      <el-select v-model="value2" clearable placeholder="请选择">
        <el-option
          v-for="item in option2"
          :key="item.libraryId"
          :label="item.libraryName"
          :value="item.libraryId">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="预约时间" prop="forwardTime">
      <el-date-picker
        v-model="value4"
        type="datetime"
        placeholder="选择预约时间">
      </el-date-picker>
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
        option2: [],
        value2: '',
        options: [],
        value: '',
        value4: '',
        visible: false,
        userName: '',
        dataForm: {
          forwardId: 0,
          forwardCode: '',
          userId: '',
          libraryId: ''
        },
        dataRule: {
          value: [
            { required: true, message: '读者不能为空', trigger: 'blur' }
          ],
          value2: [
            { required: true, message: '图书馆不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    created: function () {
      // 页面初始时加载下拉框的数据
      this.getReaderList()
      this.getLibraryName()
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
      // 创建获取后端动态图书馆类别数据的前端方法
      getLibraryName () {
        this.$http({
          url: this.$http.adornUrl('/bookmanager/libraryinfo/getLibraryNameList'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.option2 = data.list
        })
      },
      init (id) {
        this.dataForm.forwardId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.forwardId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/forwardinfo/info/${this.dataForm.forwardId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.value = data.forwardInfo.userId
                this.value2 = data.forwardInfo.libraryId
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        console.log(this.dataForm)
        this.userName = this.$cookie.get('userName')
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/forwardinfo/${!this.dataForm.forwardId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'forwardId': this.dataForm.forwardId || undefined,
                'userId': this.value,
                'libraryId': this.value2,
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
              this.value2 = ''
            })
          }
        })
      }
    }
  }
</script>
