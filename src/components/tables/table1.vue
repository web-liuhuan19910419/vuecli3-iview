<template>
  <!--类似表格中后面带有输入框的组件-->
  <div class="container-layout">
    <p class="title-layout" v-if="isMustProp"><span class="must-layout">*&nbsp;</span>{{$t(titleProp)}}</p>
    <p class="title-layout" v-else>{{$t(titleProp)}}</p>
    <div class="content-layout">
       <ul class="content-title-layout">
          <li style="width: 30px;" v-if="issurportChooseProp"></li>
          <li class="content-title-item-layout" v-for="(item, index) of gridTitleProp" :key="item.name" :style="{width: processWidths(index)}">{{$t(item.name)}}</li>
       </ul>
       <div class="content-input-layout">
          <ul class="content-grid-layout">
            <li class="content-grid-item-layout" v-for="(item, rowIndex) of gridData" :key=rowIndex>
                <div class="choose-td-layout" style="width: 30px;" v-if="issurportChooseProp"
                  @click.stop="mutiChoiceItem(rowIndex)">
                  <img
                    v-if="issurportChooseProp"
                    class="radio-button"
                    style="z-index:1"
                    :src="choiceSrc(rowIndex)">
                </div>
                <div class="content-td-layout" v-for="(element, index) of item" :key=index :style="{'width': processContentWidths(index)}">{{element}}</div>
                <div v-if="isHasInputProp">
                  <input class="input-layout" type="text" v-model="inputContent[rowIndex]" :style="{'width':inputWidthProp}" v-if="!isNumberTypeProp"/>
                  <input class="input-layout" type="number" min="0" v-model="inputContent[rowIndex]" :style="{'width':inputWidthProp}" v-if="isNumberTypeProp"/>
                </div>
            </li>
          </ul>
       </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    titleProp: '', // 名称
    isMustProp: false, // 是不是必须填的
    gridTitleProp: Array, // 表头名字
    gridDataProp: Array, // 表格的数据
    titleWidthsProp: Array, // 表头的长度
    contentWidthsProp: Array, // 表格内容的长度
    inputWidthProp: '80px',
    issurportChooseProp: false,
    isNumberTypeProp: false,
    isHasInputProp: false,
    initChooseItemProp: Array // 初始化选中选项
  },
  data () {
    return {
      titleWidths: [], // 表头的长度
      contentWidths: [],
      gridData: [], // 表格的数据
      inputContent: [],
      mutiChoiceArr: [],
      initChooseItem: []
    }
  },
  methods: {
    choiceSrc (rowIndex) {
      if (this.mutiChoiceArr === undefined || this.mutiChoiceArr[rowIndex].checked === null) {
        return ''
      } else if (this.mutiChoiceArr[rowIndex].checked === '1') {
        return require('../assets/images/radio_checked.png')
      } else if (this.mutiChoiceArr[rowIndex].checked === '0') {
        return require('../assets/images/radio_unchecked.png')
      } else {
        return require('../assets/images/radio_disabled.png')
      }
    },
    processWidths (index) {
      return this.titleWidths[index]
    },
    processContentWidths (index) {
      return this.contentWidths[index]
    },
    initMutiChoiceArr () { // 随时监听公共操作数据的选中状态，配置表格的每行数据的选中状态
      // 添加选择数据
      this.mutiChoiceArr = []
      for (let index = 0; index < this.initChooseItem.length; index++) {
        let element = {}
        if (this.initChooseItem[index].checked === '2') { // 禁止选择
          element.checked = '2'
        } else {
          element.checked = '0'
        }
        this.mutiChoiceArr.push(element)
      }
    },
    mutiChoiceItem (rowIndex) { // 单选点击操作
      if (this.issurportChooseProp === false) {
        return
      }
      let itemStatus = this.mutiChoiceArr[rowIndex].checked
      for (let index = 0; index < this.mutiChoiceArr.length; index++) {
        if (index !== rowIndex) { // 如果不是当前行就设置为没有选中
          this.mutiChoiceArr[index].checked = '0'
        }
      }
      itemStatus = itemStatus === '2' ? '2' : itemStatus === '1' ? '0' : '1'
      this.mutiChoiceArr[rowIndex].checked = itemStatus
      console.log(rowIndex, itemStatus)
      this.$emit('on-index-change', rowIndex, itemStatus)
    },
    changeInput (rowIndex) { console.log(rowIndex) }
  },
  created () {
    this.gridData = this.gridDataProp
    this.initChooseItem = this.initChooseItemProp
    console.log(this.gridData)
    this.titleWidths = this.titleWidthsProp
    this.contentWidths = this.contentWidthsProp
    console.log(this.contentWidths)
    for (let index = 0; index < this.gridData.length; index++) {
      if (this.isNumberTypeProp) {
        this.inputContent.push('0')
      } else {
        this.inputContent.push('')
      }
    }
    if (this.issurportChooseProp) { // 初始化选中按钮
      this.initMutiChoiceArr()
    }
  },
  watch: {
    'gridDataProp' (to, from) {
      this.gridData = to
      console.log(to)
      this.inputContent = []
      for (let index = 0; index < this.gridData.length; index++) {
        if (this.isNumberTypeProp) {
          this.inputContent.push(0)
        } else {
          this.inputContent.push('')
        }
      }
    },
    'titleWidthsProp' (to, from) {
      if (this.titleWidthsProp !== undefined) {
        this.titleWidths = to
      }
    },
    'contentWidthsProp' (to, from) {
      if (this.contentWidthsProp !== undefined) {
        this.contentWidths = to
      }
    },
    'inputContent': {
      handler (to, from) {
        this.$emit('input-content-change', to)
      },
      deep: true
    }
  }
}
</script>

<style scoped>
  .container-layout {
    width: 100%;
    display: flex;
  }
  .title-layout {
    width: 100px;
    height: 30px;
    line-height: 30px;
    font-size:14px;
    color: #484848;
    width: 120px;
    text-align: right;
    margin-left: 10px;
  }
  .must-layout {
    color: #E53646;
  }
  ul li{
   list-style-type:none;
  }
  .content-title-layout {
    display: flex;
  }
  .content-title-layout > li + li {
    margin-left: 10px;
  }
  .content-layout {
    padding-left: 10px;
    max-height: 250px;
    overflow-y:auto;
  }
  .content-title-item-layout {
    height: 30px;
    line-height: 30px;
    color: #95969C;
    font-weight: 500;
    font-size: 12px;
    text-align: left;
  }
  .content-input-layout {
    display: flex;
  }
  .content-grid-item-layout {
    display: flex;
  }
  .content-td-layout {
    text-align: left;
    line-height: 1.5;
    color: #37383B;
  }
  .content-grid-item-layout {
    margin-top:8px;
  }
  .content-grid-item-layout >>> div + div {
    margin-left: 10px;
    font-size: 12px;
  }
  .input-layout {
    width: 80px;
    outline: none;
    height:28px;
    background:rgba(255,255,255,1);
    border:1px solid rgba(216,219,226,1);
    border-radius:4px;
  }
  .radio-button {
  width: 10px;
  height: 10px;
}
</style>
