<template>
  <el-dialog
    :title="!dataForm.topicId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="题目" prop="title">
      <el-input v-model="dataForm.title" placeholder="题目"></el-input>
    </el-form-item>
    <el-form-item label="选项A" prop="optiona">
      <el-input v-model="dataForm.optiona" placeholder="选项A"></el-input>
    </el-form-item>
    <el-form-item label="选项B" prop="optionb">
      <el-input v-model="dataForm.optionb" placeholder="选项B"></el-input>
    </el-form-item>
    <el-form-item label="选项C" prop="optionc">
      <el-input v-model="dataForm.optionc" placeholder="选项C"></el-input>
    </el-form-item>
    <el-form-item label="选项D" prop="optiond">
      <el-input v-model="dataForm.optiond" placeholder="选项D"></el-input>
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
          topicId: 0,
          title: '',
          optiona: '',
          optionb: '',
          optionc: '',
          optiond: '',
          deleted: '',
          insertTime: '',
          insertName: '',
          updateTime: '',
          updateName: ''
        },
        dataRule: {
          title: [
            { required: true, message: '题目不能为空', trigger: 'blur' }
          ],
          optiona: [
            { required: true, message: '选项A不能为空', trigger: 'blur' }
          ],
          optionb: [
            { required: true, message: '选项B不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.topicId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.topicId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/topic/info/${this.dataForm.topicId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.title = data.topic.title
                this.dataForm.optiona = data.topic.optiona
                this.dataForm.optionb = data.topic.optionb
                this.dataForm.optionc = data.topic.optionc
                this.dataForm.optiond = data.topic.optiond
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
              url: this.$http.adornUrl(`/bookmanager/topic/${!this.dataForm.topicId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'topicId': this.dataForm.topicId || undefined,
                'title': this.dataForm.title,
                'optiona': this.dataForm.optiona,
                'optionb': this.dataForm.optionb,
                'optionc': this.dataForm.optionc,
                'optiond': this.dataForm.optiond
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
