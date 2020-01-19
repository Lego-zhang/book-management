<template>
  <div class="upload-container">
    <el-upload
      :action="action"
      :headers="headers"
    >请上传</el-upload>
  </div>
</template>

<script>
import { getToken } from '../../utils/auth'
export default {
  components: {},
  props: {},
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
  methods: {}
}
</script>
<style lang="scss" scoped>
</style>
