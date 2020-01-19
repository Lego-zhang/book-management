<template>
  <div class="upload-container">
    <!-- multiple：一次上传多本 -->
    <!--limit：一次上传一本 -->

    <el-upload
      :action="action"
      :headers="headers"
      :multiple="false"
      :limit="1"
      :before-upload="beForeUpload"
      :on-success="onSuccess"
      :on-error="onError"
      :on-remove="onRemove"
      :file-list="fileList"
      :on-exceed="onExceed"
      :disabled="disabled"
      drag
      show-file-list
      accept="application/epub+zip"
      class="image-upload"
    >
      <i class="el-icon-upload" />
      <div
        v-if="fileList.length===0"
        class="el-upload__text"
      >
        请将电子书拖入或<em>点击上传</em>
      </div>
      <div
        v-else
        class="el-upload__text"
      >
        图书已上传
      </div>
      <!-- multiple：一次上传多本 -->
      <!--limit：一次上传一本 -->
      <!-- before-upload:图书上传前 -->
      <!-- on-success：图书上传成功 -->
      <!-- on-error：图书上传失败 -->
      <!-- on-remove: 上传成功后删除电子书 -->
      <!-- file-list：文件列表 -->
      <!-- on-exceed: 当上传超过(limit数值)本书的时候会调用-->
      <!-- drag:可以拖拽上传 -->

    </el-upload>
  </div>
</template>

<script>
import { getToken } from '../../utils/auth'
export default {
  components: {},
  props: {
    fileList: {
      type: Array,
      default() {
        return []
      }
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      action: `${process.env.VUE_APP_BASE_API}/book/upload`

    }
  },
  computed: {
    headers() {
      return {
        Authorization: `Bearer ${getToken()}`
      }
    }
  },
  watch: {},
  created() { },
  mounted() { },
  methods: {
    beForeUpload(file) {
      // 文件上传之前
      console.log(file)
      this.$emit('beforeUpload', file)
    },
    onSuccess() { },
    onError(err) {
      const errMsg = err.message && JSON.parse(err.message)
      this.$message({
        message: (errMsg && `上传失败，失败原因：${errMsg.msg}`) || '上传失败',
        type: 'error'
      })
      this.$emit('onError', err)
    },
    onRemove() { },
    onExceed() {
      this.$$message({
        message: '每次只能上传一本电子书',
        type: 'warning'
      })
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
