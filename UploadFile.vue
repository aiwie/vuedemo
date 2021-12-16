<template>
  <!-- 文件上传组件 -->
  <div>
    <el-upload
      :action="$store.state.baseURL + url"
      :list-type="params.listType"
      :limit="params.limit"
      :on-change="change"
      :on-success="success"
      :on-remove="remove"
      ref="upload"
      :before-upload="beforeupload"
      :class="{ hides: hideUpload }"
      :accept="params.accept"
      :file-list="fileList"
    >
      <el-button v-if="params.listType == 'text'" size="small" type="primary"
        >点击上传</el-button
      >
      <i
        v-if="params.listType == 'picture-card' && isIdCard.idCard == false"
        class="el-icon-plus"
      ></i>
      <div
        class="idCard"
        v-if="params.listType == 'picture-card' && isIdCard.idCard == true"
      >
        <img v-if="isIdCard.isCardZ == true" src="@/assets/img/z.jpeg" alt="" />
        <img
          v-if="isIdCard.isCardZ == false"
          src="@/assets/img/f.jpeg"
          alt=""
        />
      </div>
    </el-upload>
  </div>
</template>

<script>
// accept ".jpg,.jpeg,.png,.gif,.bmp,.pdf,.JPG,.JPEG,.PBG,.GIF,.BMP,.PDF" 文件格式
export default {
  props: {
    uploadConfig: {
      type: Object
    },
    url: {
      type: String
      //  default:this.store.baseURL+ "/file-service/upload/uploadfiles"
    },
    isIdCard: {
      type: Object,
      default: () => {
        return {
          idCard: false, //为了给背景图片
          isCardZ: true //区分图片正反面
        }
      }
    }
  },
  data() {
    return {
      hideUpload: false,
      fileList: []
    }
  },
  computed: {
    params() {
      return Object.assign(
        {
          listType: 'text',
          limit: 1,
          accept: '.zip,.xlsx',
          size: '50'
        },
        this.uploadConfig
      )
    }
  },
  methods: {
    success(res) {
      if (res.code === 1) {
        const { file } = res.data
        this.$emit('imgSuccess', file)
        this.$message.success('上传成功')
      } else {
        this.fileList = []
        // let resData = res.data?.errorMeagess[0] || ""
        this.$message.error(res.msg || '上传失败')
      }
    },
    remove(res, fileList) {
      this.hideUpload = fileList.length >= this.params.limit
      this.$emit('imgRemove',fileList)
    },
    change(file, fileList) {
      this.hideUpload = fileList.length >= this.params.limit
    },
    clear() {
      this.$refs.upload.clearFiles()
    },
    beforeupload(file) {
      const isLt20M = file.size / 1024 / 1024 < this.params.size
      if (!isLt20M) {
        this.$message({
          message: `上传文件大小不能超过 ${this.params.size}MB!`,
          type: 'warning'
        })
      }
      return isLt20M
    }
  },
  watch: {
    fileList: function () {
      this.hideUpload = this.fileList.length >= this.params.limit
    }
  }
}
</script>

<style lang="less" scoped>
.hides {
  /deep/.el-upload--picture-card {
    display: none;
  }
}
.idCard {
  width: 100%;
  img {
    width: 100%;
    // height: 145px;
  }
}
</style>
