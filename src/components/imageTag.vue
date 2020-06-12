<template>
  <div class="edit-image">
    <div class="operation-wrapper">
      <el-button type="text"
                 v-for="obj in operationList"
                 :class="activeId===obj.id?'active':''"
                 :title="obj.name"
                 @click="shapeToggle(obj.id)">
        <svg-icon :icon-class="obj.icon"></svg-icon>
      </el-button>
      <el-divider direction="vertical"></el-divider>
      <el-button type="text" :disabled="scaleIndex===0" @click="scale(false)">
        <svg-icon icon-class="zoomOut"></svg-icon>
      </el-button>
      <el-button type="text" :disabled="scaleIndex===10" @click="scale(true)">
        <svg-icon icon-class="zoomIn"></svg-icon>
      </el-button>
      <el-divider direction="vertical"></el-divider>
      <el-button type="text" @click="deleteTag">
        <svg-icon icon-class="eraser"></svg-icon>
      </el-button>
    </div>
    <div class="style-config" v-if="activeId">
      <span class="stroke-width" v-if="activeId<4">
        <span :class="currentEdit.strokeWidth===2?'checked':''"
              @click="changeStrokeWidth(2)">
          <span class="dot mini"></span>
        </span>
        <span :class="currentEdit.strokeWidth===4?'checked':''"
              @click="changeStrokeWidth(4)">
          <span class="dot middle"></span></span>
        <span :class="currentEdit.strokeWidth===8?'checked':''"
              @click="changeStrokeWidth(8)">
          <span class="dot large"></span></span>
      </span>
      <span class="font-size-select" v-if="activeId===4">
        <el-select v-model="fontSize" size="mini" @change="changeFontSize">
          <el-option v-for="size in fontSizeList" :key="size" :value="size">{{size}}</el-option>
        </el-select>
      </span>
      <span class="stroke-color">
        <span class="checked" :style="{backgroundColor: checkedColor}"></span>
        <span class="check-group">
          <span v-for="(color,i) in colorList"
                :key="i"
                :style="{backgroundColor: color}"
                @click="changeColor(color)"></span>
        </span>
      </span>
    </div>
    <div class="main">
      <div id="svg-container" :style="{width: `${scaleList[scaleIndex].width}px`,
        height: `${scaleList[scaleIndex].height}px`,
        left: `${scaleList[scaleIndex].scale < 1 ? '50%' : 'inherit'}`,
        transform: `${scaleList[scaleIndex].scale < 1 ? 'translateX(-50%)':'inherit'}`}"></div>
    </div>
    <div id="textInput" class="text-input"
         contenteditable="true"
         :style="{
           color: currentEdit.text.color,
           fontSize: `${currentEdit.text.fontSize}px`
           }"></div>
  </div>
</template>

<script>
import $ from 'jquery'
import { drawInit, toggleDrawingMode, changeScale,
  scaleGraphics, deleteTags, changeImageBg } from '@/js/draw'
import { mapState, mapActions } from 'vuex'

const width = $('.main').width();
const height = 500;
export default {
  name: 'HelloWorld',
  props: {
    colorList: Array,
    fontSizeList: Array,
  },
  data() {
    return {
      operationList: [
        {
          id: 1,
          name: '箭头',
          icon: 'path'
        },
        {
          id: 2,
          name: '方框',
          icon: 'rect'
        },
        {
          id: 3,
          name: '圆形',
          icon: 'ellipse'
        },
        {
          id: 4,
          name: '文本',
          icon: 'text'
        },
      ],
      fontSize: '',
      checkedColor: '',
      scaleList: [
        {
          width: width * 0.2,
          height: height * 0.2 - 36,
          scale: 0.2
        },
        {
          width: width * 0.3,
          height: height * 0.3 - 36,
          scale: 0.3
        },
        {
          width: width * 0.5,
          height: height * 0.5 - 36,
          scale: 0.5
        },
        {
          width: width * 0.8,
          height: height * 0.8 - 36,
          scale: 0.8
        },
        {
          width: width * 0.9,
          height: height * 0.9 - 36,
          scale: 0.9
        },
        {
          width: width,
          height: height - 40,
          scale: 1
        },
        {

          width: width * 1.5,
          height: height * 1.5 - 36,
          scale: 1.5
        },
        {

          width: width * 2,
          height: height * 2 - 36,
          scale: 2
        },
        {

          width: width * 2.5,
          height: height * 2.5 - 36,
          scale: 2.5
        },
        {

          width: width * 3,
          height: height * 3 - 36,
          scale: 3
        },
        {

          width: width * 4,
          height: height * 4 - 36,
          scale: 4
      }
      ],
    };
  },
  methods: {
    ...mapActions('editImage', [
      'updateStrokeWidth',
      'updateColor',
      'updateFontSize',
      'updateImageList',
      'updateScaleId',
      'updateActiveId',
    ]),
    shapeToggle (shapeId) {
      this.updateActiveId(shapeId);
      toggleDrawingMode(shapeId);
      switch (shapeId) {
        case 1:
          this.checkedColor = this.currentEdit.pathColor;
          break;
        case 2:
          this.checkedColor = this.currentEdit.rectColor;
          break;
        case 3:
          this.checkedColor = this.currentEdit.ellipseColor;
          break;
        case 4:
          this.checkedColor = this.currentEdit.text.color;
          this.fontSize = this.currentEdit.text.fontSize;
          break;
        default:
          break
      }
    },
    changeStrokeWidth (width) {
      this.updateStrokeWidth(width)
    },
    changeColor (color) {
      this.checkedColor = color;
      this.updateColor({ id : this.activeId, color })
    },
    changeFontSize () {
      this.updateFontSize(this.fontSize)
    },
    scale(isAdd) {
      this.updateScaleId(isAdd);
      changeScale(this.scaleList[this.scaleIndex].scale);
      scaleGraphics(this.scaleList[this.scaleIndex].scale)
    },
    deleteTag () {
      deleteTags()
    }
  },
  computed: {
    ...mapState('editImage', {
      currentEdit: state => state.currentEdit,
      currentSvg: state => state.currentEdit,
      scaleIndex: state => state.currentEdit.scaleIndex,
      activeId: state => state.currentEdit.activeId,
    }),
  }
}
</script>

