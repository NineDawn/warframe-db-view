<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="英文名称" prop="en">
      <el-input v-model="dataForm.en" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="中文名称" prop="zh">
      <el-input v-model="dataForm.zh" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="图片地址" prop="thumb">
      <el-input v-model="dataForm.thumb" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="市场id" prop="marketid">
      <el-input v-model="dataForm.marketid" placeholder=""></el-input>
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
          en: '',
          zh: '',
          thumb: '',
          marketid: ''
        },
        dataRule: {
          en: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          zh: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          thumb: [
            { required: true, message: '不能为空', trigger: 'blur' }
          ],
          marketid: [
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
              url: this.$http.adornUrl(`/app/wfsale/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.en = data.wfSale.en
                this.dataForm.zh = data.wfSale.zh
                this.dataForm.thumb = data.wfSale.thumb
                this.dataForm.marketid = data.wfSale.marketid
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
              url: this.$http.adornUrl(`/app/wfsale/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'en': this.dataForm.en,
                'zh': this.dataForm.zh,
                'thumb': this.dataForm.thumb,
                'marketid': this.dataForm.marketid
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
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
