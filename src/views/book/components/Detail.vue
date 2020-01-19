<template>
  <div>
    <el-form
      ref="postForm"
      :model="postForm"
    >
      <sticky :class-name="'sub-navbar ' + postForm.status">
        <el-button
          v-if="!isEdit"
          @click="showGuide"
        >显示帮助</el-button>
        <el-button
          v-loading="loading"
          type="success"
          style="margin-left:10px"
          @click="submitForm"
        >{{ isEdit?'编辑电子书':'新增电子书' }}</el-button>
      </sticky>
      <div class="detail-container">
        <el-row>
          <warning />
          <el-col :span="24">
            <ebook-upload />
            <!-- 表单控件的具体样式 -->
          </el-col>
          <el-col :span="24">
            <!-- 表单控件的具体样式 -->
          </el-col>
        </el-row>
      </div>
    </el-form>
  </div>
</template>

<script>
import Sticky from '../../../components/Sticky/index.vue'
import Warning from './Warning'
import EbookUpload from '../../../components/EookUppload/index'
export default {
  components: {
    Sticky,
    Warning,
    EbookUpload
  },
  props: {
    isEdit: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      loading: false,
      postForm: {
        status: 'draft'
      }
    }
  },
  methods: {
    showGuide() {
      console.log('show Guide ...')
    },
    submitForm() {
      this.loading = true
      setTimeout(() => {
        this.loading = false
      }, 1000)
    }
  }
}
</script>
<style lang="scss" scoped>
.detail-container{
  padding: 40px 50px 20px;
}
</style>
