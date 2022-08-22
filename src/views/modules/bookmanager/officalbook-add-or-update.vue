<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="ISBN编码" prop="isbn">
      <el-input v-model="dataForm.isbn" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图书名" prop="bookName">
      <el-input v-model="dataForm.bookName" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图书出版社" prop="bookPress">
      <el-input v-model="dataForm.bookPress" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="作者" prop="author">
      <el-input v-model="dataForm.author" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="出版时间" prop="pressTime">
      <el-input v-model="dataForm.pressTime" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="定价" prop="pricing">
      <el-input v-model="dataForm.pricing" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="分类" prop="category">
      <el-input v-model="dataForm.category" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="开本" prop="format">
      <el-input v-model="dataForm.format" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="页数" prop="pagination">
      <el-input v-model="dataForm.pagination" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="正文语种" prop="language">
      <el-input v-model="dataForm.language" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图书照片地址" prop="imageUrl">
      <el-input v-model="dataForm.imageUrl" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图书txt文件地址" prop="booktxtUrl">
      <el-input v-model="dataForm.booktxtUrl" placeholder=""></el-input>
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
          id: 0,
          isbn: '',
          bookName: '',
          bookPress: '',
          author: '',
          pressTime: '',
          pricing: '',
          category: '',
          format: '',
          pagination: '',
          language: '',
          imageUrl: '',
          booktxtUrl: ''
        },
        dataRule: {
          isbn: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          bookName: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          bookPress: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          author: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          pressTime: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          pricing: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          category: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          format: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          pagination: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          language: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          imageUrl: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          booktxtUrl: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/officalbook/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.isbn = data.officalBook.isbn
                this.dataForm.bookName = data.officalBook.bookName
                this.dataForm.bookPress = data.officalBook.bookPress
                this.dataForm.author = data.officalBook.author
                this.dataForm.pressTime = data.officalBook.pressTime
                this.dataForm.pricing = data.officalBook.pricing
                this.dataForm.category = data.officalBook.category
                this.dataForm.format = data.officalBook.format
                this.dataForm.pagination = data.officalBook.pagination
                this.dataForm.language = data.officalBook.language
                this.dataForm.imageUrl = data.officalBook.imageUrl
                this.dataForm.booktxtUrl = data.officalBook.booktxtUrl
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
              url: this.$http.adornUrl(`/bookmanager/officalbook/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'isbn': this.dataForm.isbn,
                'bookName': this.dataForm.bookName,
                'bookPress': this.dataForm.bookPress,
                'author': this.dataForm.author,
                'pressTime': this.dataForm.pressTime,
                'pricing': this.dataForm.pricing,
                'category': this.dataForm.category,
                'format': this.dataForm.format,
                'pagination': this.dataForm.pagination,
                'language': this.dataForm.language,
                'imageUrl': this.dataForm.imageUrl,
                'booktxtUrl': this.dataForm.booktxtUrl
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
