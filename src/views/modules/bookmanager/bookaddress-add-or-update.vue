<template>
  <el-dialog
    :title="!dataForm.adressId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
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
    <el-form-item label="图书类别" prop="classifyName">
      <el-select v-model="value1" multiple  :multiple-limit="1" placeholder="请选择(可多选,最大为5)">
        <el-option
          v-for="item in options"
          :key="item.categoryId"
          :label="item.categoryCode+item.categoryName"
          :value="item.categoryId">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="门号" prop="door">
      <el-input v-model="dataForm.door" placeholder="门号"></el-input>
    </el-form-item>
    <el-form-item label="书架数" prop="pressmark">
      <el-input v-model="dataForm.pressmark" placeholder="书架号"></el-input>
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
        options: [],
        value1: '',
        option2: [],
        value2: '',
        userName: '',
        dataForm: {
          adressId: 0,
          libraryId: '',
          door: '',
          pressmark: ''
        },
        dataRule: {
          value1: [
            { required: true, message: '图书分类不能为空', trigger: 'blur' }
          ],
          value2: [
            { required: true, message: '图书馆不能为空', trigger: 'blur' }
          ],
          door: [
            { required: true, message: '门号不能为空', trigger: 'blur' }
          ],
          pressmark: [
            { required: true, message: '书架号不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    created: function () {
      this.getClassifyName()
      this.getLibraryName()
    },
    methods: {
      // 创建获取后端动态图书类别数据的前端方法
      getClassifyName () {
        console.log('这个事件点击了')
        this.$http({
          url: this.$http.adornUrl('bookmanager/category/getCategoryNameList'),
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
        this.dataForm.adressId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.adressId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/bookaddress/info/${this.dataForm.adressId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.value2 = data.bookAddress.libraryId
                this.dataForm.door = data.bookAddress.door
                this.dataForm.pressmark = data.bookAddress.pressmark
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.userName = this.$cookie.get('userName')
        console.log(this.value1)
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/bookaddress/${!this.dataForm.adressId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'adressId': this.dataForm.adressId || undefined,
                'libraryId': this.value2,
                'categoryId': this.value1 + '',
                'door': this.dataForm.door,
                'pressmark': this.dataForm.pressmark,
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
