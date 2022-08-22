<template>
  <el-dialog
    :title="!dataForm.bookId ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="图书名称" prop="bookName">
      <el-input v-model="dataForm.bookName" placeholder="图书名称"></el-input>
    </el-form-item>
    <el-form-item label="图书出版社" prop="bookPress">
      <el-input v-model="dataForm.bookPress" placeholder="图书出版社"></el-input>
    </el-form-item>
    <el-form-item label="图书类别" prop="categoryName">
      <el-input v-model="dataForm.categoryName" placeholder="图书分类"></el-input>
    </el-form-item>
    <el-form-item label="所属图书馆" prop="libraryName">
      <el-input v-model="dataForm.libraryName" placeholder="图书馆"></el-input>
    </el-form-item>
    <el-form-item label="图书的馆存位置">
      <el-select v-model="doorValue" clearable placeholder="请选择门号">
        <el-option
          v-for="item in door"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
      <el-select v-model="rowValue" clearable placeholder="请选择书架行号">
        <el-option
          v-for="item in row"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
      <el-select v-model="lineValue" clearable placeholder="请选择书架列号">
        <el-option
          v-for="item in line"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
    </el-form-item>
    <el-form-item label="图书照片">
      <el-upload
        :action="requestUrl"
        list-type="picture-card"
        :before-upload="beforeUpload"
        ref="newUpload"
        :auto-upload= false
        :headers="{ 'token': token }"
        :limit="1"
        :on-success="successUpload">
        <i class="el-icon-plus"></i>
      </el-upload>
      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" alt="">
      </el-dialog>
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
        requestUrl: 'http://localhost:8018/bookmanager/bookinfo/saveImageUrl',
        doorValue: '',
        door: [],
        rowValue: '',
        imageFile: '',
        token: '',
        row: [],
        lineValue: '',
        line: [],
        imageName: '',
        dialogVisible: false,
        userName: '',
        option2: [],
        value2: '',
        options: [],
        classify: [],
        value1: [],
        visible: false,
        dataForm: {
          bookId: 0,
          bookName: '',
          bookPress: '',
          categoryName: '',
          libraryName: ''
        },
        dataRule: {
          bookName: [
            { required: true, message: '图书名称不能为空', trigger: 'blur' }
          ],
          bookPress: [
            { required: true, message: '图书出版社不能为空', trigger: 'blur' }
          ],
          categoryName: [
            { required: true, message: '图书分类不能为空', trigger: 'blur' }
          ],
          libraryName: [
            { required: true, message: '图书馆不能为空', trigger: 'blur' }
          ]
        },
        categoryCode: '',
        addressId: '',
        upBool: false,
        file: ''
      }
    },
    created: function () {
      // 页面初始时加载下拉框的数据
      this.getClassifyName()
      this.getLibraryName()
      console.log('token', this.$cookie.get('token'))
      this.token = this.$cookie.get('token')
    },
    methods: {
      // 上传照片的方法
      beforeUpload (file) {
        // this.upBool = true
        // const isLt2M = file.size < 1024 * 1024 * 0.5
        // if (!isLt2M) {
        //   this.$message.error('上传文件大小不能超过500kb!')
        // } else {
        //   let _URL = window.URL || window.webkitURL
        //   let img = new Image()
        //   img.src = _URL.createObjectURL(file)
        // }
        // return isLt2M
      },
      successUpload (res, file) {
        // 成功上传了
        console.log(file)
        this.file = file
        // if (res.code === 0) {
        //   this.imageUrl = res.imageUrl
        //   this.$message({
        //     message: res.masg,
        //     type: 'success'
        //   })
        // }
        // 这个时候再调用一下修改提交接口
        this.dataFormSubmit()
        // 再调一下删除图片的接口
      },
      // 创建后端根据前端提供的图书类别已经图书馆分配图书的馆存地址
      getBookAddress () {
      // 首先判断图书的类别是否已经选择了
        if (this.categoryName !== '') {
          // 当图书馆类别和图书馆都选择的时候，调用后端自动分配图书存放地址的方法
          this.$http({
            url: this.$http.adornUrl('bookmanager/bookaddress/getBookAddress'),
            method: 'get',
            params: this.$http.adornParams({
              'categoryName': this.dataForm.categoryName,
              'libraryName': this.dataForm.libraryName
            })
          }).then(({data}) => {
            console.log(data)
            this.doorValue = data.map.door
            this.rowValue = data.map.row
            this.lineValue = data.map.line
            this.addressId = data.map.addressId
          })
        } else {
          console.log('执行了这一步')
          this.$alert('请先选择图书类别', {
            confirmButtonText: '确定',
            callback: actio => {
              this.$message({
                type: 'info'
              })
            }
          })
          // 清空当前所选的图书馆
          this.value2 = ''
        }
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
      // 创建获取后端动态图书类别数据的前端方法
      getClassifyName () {
        this.$http({
          url: this.$http.adornUrl('/bookmanager/classify/getClassifyNameList'),
          method: 'get',
          params: this.$http.adornParams()
        }).then(({data}) => {
          this.options = data.list
        })
      },
      init (id) {
        console.log('id', id)
        this.dataForm.bookId = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          console.log(this.dataForm.bookId)
          if (this.dataForm.bookId) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/bookinfo/info/${this.dataForm.bookId}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                console.log('bookInfo', data.bookInfo)
                this.dataForm.bookName = data.bookInfo.bookName
                this.dataForm.bookPress = data.bookInfo.bookPress
                this.dataForm.categoryName = data.bookInfo.categoryName
                this.dataForm.libraryName = data.bookInfo.libraryName
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        // 当数据修改成功的时候，将图片也确定上传
        this.$refs.newUpload.submit()
        // 使用-拼接书籍类别
        this.userName = this.$cookie.get('userName')
        this.$refs['dataForm'].validate((valid) => {
          let imageUrl = this.file.response.imageUrl
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/bookmanager/bookinfo/${!this.dataForm.bookId ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'bookId': this.dataForm.bookId || undefined,
                'bookName': this.dataForm.bookName,
                'bookPress': this.dataForm.bookPress,
                'libraryId': this.dataForm.libraryId,
                'categoryCode': this.dataForm.categoryCode,
                'userName': this.userName,
                'addressId': this.addressId,
                'image': imageUrl
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
              // 清空图书数据
              this.file = ''
            })
          }
        })
      }
    }
  }
</script>
