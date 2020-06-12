<template>
  <div class="container">
    <el-upload
      class="upload-wrapper"
      action="https://jsonplaceholder.typicode.com/posts/"
      list-type="picture-card"
      :auto-upload="false">
      <i slot="default" class="el-icon-plus"></i>
      <div slot="file" slot-scope="{file}" @click="changeImage(file)">
        <img
          class="el-upload-list__item-thumbnail"
          :src="file.url" alt="">
        <span class="el-upload-list__item-actions">
        <span
          class="el-upload-list__item-preview"
          @click="handlePictureCardPreview(file)">
          <i class="el-icon-zoom-in"></i>
        </span>
      </span>
      </div>
    </el-upload>
    <image-tag :colorList="colorList"
               :fontSizeList="fontSizeList"
               :active="active"
    ></image-tag>
    <el-dialog :visible.sync="dialogVisible">
      <img width="100%" :src="dialogImageUrl" alt="">
    </el-dialog>
  </div>
</template>

<script>
import imageTag from '@/components/imageTag.vue'
import { scaleGraphics, changeImageBg } from '@/js/draw'
import { mapActions } from 'vuex'

export default {
  name: 'Home',
  components: {
    imageTag
  },
  data() {
    return {
      dialogImageUrl: '',
      dialogVisible: false,
      disabled: false,
      active: 0,
      colorList: [
        '#ea4c72', '#f5b225', '#48c486', '#4386f5', '#000000', '#ffffff'
      ],
      fontSizeList: [8, 9, 10, 12, 14, 16, 18, 20, 22],
    }
  },
  methods: {
    ...mapActions('editImage', [
      'updateImageList',
    ]),
    changeImage (file) {
      changeImageBg(file);
      this.updateImageList(file);
      scaleGraphics();
      this.active = 0;
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
  }
}
</script>
