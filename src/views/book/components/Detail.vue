<template>
  <div>
    <el-form
      ref="postForm"
      :model="postForm"
      :rules="rules"
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
            <ebook-upload
              :file-list="fileList"
              :disabled="isEdit"
              @onSuccess="onUploadSuccess"
              @onRemove="onUploadRemove"
            />
            <!-- 表单控件的具体样式 -->
          </el-col>
          <el-col :span="24">
            <el-col :span="24">
              <el-form-item
                style="margin-bottom: 40px;"
                prop="title"
              >
                <MDinput
                  v-model="postForm.title"
                  :maxlength="100"
                  name="name"
                  required
                >
                  书名
                </MDinput>
              </el-form-item>
              <div>
                <el-row>
                  <el-col
                    :span="12"
                    class="form-item-author"
                  >
                    <el-form-item
                      :label-width="labelWidth"
                      label="作者："
                      prop="author"
                    >
                      <el-input
                        v-model="postForm.author"
                        placeholder="作者"
                        style="width: 100%"
                      />
                    </el-form-item>
                  </el-col>
                  <el-col :span="12">
                    <el-form-item
                      :label-width="labelWidth"
                      label="出版社："
                      prop="publisher"
                    >
                      <el-input
                        v-model="postForm.publisher"
                        placeholder="出版社"
                        style="width: 100%"
                      />
                    </el-form-item>
                  </el-col>
                </el-row>
                <el-row>
                  <el-col :span="12">
                    <el-form-item
                      prop="language"
                      :label-width="labelWidth"
                      label="语言："
                    >
                      <el-input
                        v-model="postForm.language"
                        placeholder="语言"
                        style="width: 100%"
                      />
                    </el-form-item>
                  </el-col>
                  <el-col :span="12">
                    <el-form-item
                      :label-width="labelWidth"
                      label="根文件："
                    >
                      <el-input
                        v-model="postForm.rootFile"
                        placeholder="根文件"
                        style="width: 100%"
                        disabled
                      />
                    </el-form-item>
                  </el-col>
                </el-row>
                <el-row>
                  <el-col :span="12">
                    <el-form-item
                      :label-width="labelWidth"
                      label="文件路径："
                    >
                      <el-input
                        v-model="postForm.filePath"
                        placeholder="文件路径"
                        style="width: 100%"
                        disabled
                      />
                    </el-form-item>
                  </el-col>
                  <el-col :span="12">
                    <el-form-item
                      :label-width="labelWidth"
                      label="解压路径："
                    >
                      <el-input
                        v-model="postForm.unzipPath"
                        placeholder="解压路径"
                        style="width: 100%"
                        disabled
                      />
                    </el-form-item>
                  </el-col>
                </el-row>
                <el-row>
                  <el-col :span="12">
                    <el-form-item
                      :label-width="labelWidth"
                      label="封面路径："
                    >
                      <el-input
                        v-model="postForm.coverPath"
                        placeholder="封面路径"
                        style="width: 100%"
                        disabled
                      />
                    </el-form-item>
                  </el-col>
                  <el-col :span="12">
                    <el-form-item
                      :label-width="labelWidth"
                      label="文件名称："
                    >
                      <el-input
                        v-model="postForm.originalName"
                        placeholder="文件名称"
                        style="width: 100%"
                        disabled
                      />
                    </el-form-item>
                  </el-col>
                </el-row>
                <el-row>
                  <el-col :span="24">
                    <el-form-item
                      label-width="60px"
                      label="封面："
                    >
                      <a
                        v-if="postForm.cover"
                        :href="postForm.cover"
                        target="_blank"
                      >
                        <img
                          :src="postForm.cover"
                          class="preview-img"
                        >
                      </a>
                      <span v-else>无</span>
                    </el-form-item>
                  </el-col>
                </el-row>
                <el-row>
                  <el-col :span="24">
                    <el-form-item
                      label-width="60px"
                      label="目录："
                    >
                      <div
                        v-if="postForm.contents && postForm.contents.length > 0"
                        class="contents-wrapper"
                      >
                        <el-tree
                          :data="contentsTree"
                          @node-click="onContentClick"
                        />
                      </div>
                      <span v-else>无</span>
                    </el-form-item>
                  </el-col>
                </el-row>
              </div>
            </el-col>
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
import MDinput from '../../../components/MDinput/index'

const defaultForm = {
  title: '',
  author: '',
  publisher: '',
  language: '',
  rootFile: '',
  cover: '',
  originalName: '',
  url: '',
  fileName: '',
  coverPath: '',
  filePath: '',
  unzipPath: ''
}

const fields = {
  title: '书名',
  author: '作者',
  language: '语言',
  publisher: '出版社'

}
export default {
  components: {
    Sticky,
    Warning,
    EbookUpload,
    MDinput
  },
  props: {
    isEdit: {
      type: Boolean,
      default: true
    }
  },
  data() {
    const validateRequire = (rule, value, callback) => {
      console.log('rule', rule, 'value', value)
      if (value.length === 0) {
        callback(new Error(fields[rule.field] + '必须填写'))
      } else {
        callback()
      }
    }
    return {
      loading: false,
      postForm: {
        status: 'draft'
      },
      fileList: [],
      labelWidth: '120px',
      contentsTree: [],
      rules: {
        title: [{
          validator: validateRequire
        }],
        author: [{
          validator: validateRequire
        }],
        language: [{
          validator: validateRequire
        }],
        publisher: [{
          validator: validateRequire
        }]
      }
    }
  },
  methods: {
    onContentClick(data) {
      if (data.text) {
        window.open(data.text)
      }
    },
    setData(data) {
      const {
        title,
        author,
        publisher,
        language,
        rootFile,
        cover,
        originalName,
        url,
        contents,
        contentsTree,
        fileName,
        coverPath,
        filePath,
        unzipPath
      } = data
      this.postForm = {
        ...this.postForm,
        title,
        author,
        publisher,
        language,
        rootFile,
        cover,
        url,
        originalName,
        contents,
        contentsTree,
        fileName,
        coverPath,
        filePath,
        unzipPath
      }
      this.contentsTree = contentsTree
    },
    setDefault() {
      this.postForm = Object.assign({}, defaultForm)
      this.contentsTree = []
    },
    onUploadSuccess(data) {
      this.setData(data)
    },
    onUploadRemove() {
      this.setDefault()
    },
    showGuide() {
    },
    submitForm() {
      if (!this.loading) {
        this.loading = true
        this.$refs.postForm.validate((valid, fields) => {
          console.log(valid, fields)
          if (valid) {
            // 111
          } else {
            const { message } = fields[Object.keys(fields)[0]][0]
            this.$message({
              message, type: 'error'
            })
            this.loading = false
          }
        })
      }
    }
  }
}
</script>
<style lang="scss" scoped>
.detail-container {
  padding: 40px 50px 20px;
  .preview-img {
    width: 200px;
    height: 270px;
  }
}
</style>
