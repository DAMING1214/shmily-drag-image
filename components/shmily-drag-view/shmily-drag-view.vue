<template>
  <view class="con">
    <movable-area class="area" :style="{ height: areaHeight }">
      <block v-for="(item, index) in dataList" :key="item[idName]">
        <movable-view
          class="view"
          :x="item.x"
          :y="item.y"
          direction="all"
          @change="onChange($event, item)"
          @touchstart="touchstart(item)"
          @mousedown="touchstart(item)"
          @touchend="touchend(item)"
          @mouseup="touchend(item)"
          :style="{ width: viewWidth + 'rpx', height: viewHeight + 'rpx', 'z-index': item.zIndex, opacity: item.opacity }"
        >
          <view class="area-con" :style="{ width: childWidth, height: childHeight, lineHeight: childHeight, transform: 'scale(' + item.scale + ')' }" v-text="item[titleName]"></view>
        </movable-view>
      </block>
      <!-- 保留 -->
      <view
        class="add"
        :style="{ width: viewWidth + 'rpx', height: viewHeight + 'rpx' }"
      >
      </view>
    </movable-area>
  </view>
</template>

<script>
export default {
  data() {
    return {
      dataList: [],
      itemWidth: 0,
      itemHeight: 0,
      cols: 0,
    }
  },
  props: {
    // 返回排序后数组
    list: {
      type: Array,
      default: function() {
        return []
      }
    },
    // 父容器宽度（实际显示的宽度为 viewWidth / 1.1 ），单位 rpx
    viewWidth: {
      type: Number,
      default: 230
    },
    // 父容器高度（实际显示的高度为 viewHeight / 1.2 ），单位 rpx
    viewHeight: {
      type: Number,
      default: 100
    },
    // id属性名(不可重复)
    idName: {
      type: String,
      default: 'id'
    },
    // 标题属性名
    titleName: {
      type: String,
      default: 'title'
    },
  },
  computed: {
    areaHeight() {
      return Math.ceil(this.dataList.length / this.cols) * this.viewHeight + 'rpx'
    },
    childWidth() {
      return this.viewWidth / 1.1 - 2 + 'rpx'
    },
    childHeight() {
      return this.viewHeight / 1.2 - 2 + 'rpx'
    },
  },
  mounted() {
    const query = uni.createSelectorQuery().in(this)
    query.select('.add').boundingClientRect(data => {
      this.itemWidth = data.width
      this.itemHeight = data.height
    })
    query.select('.con').boundingClientRect(data => {
      this.cols = Math.floor(data.width / this.itemWidth)
    })
    query.exec(() => {
      for (let i = 0; i < this.list.length; i++) {
        let absX = this.dataList.length % this.cols
        let absY = Math.floor(this.dataList.length / this.cols)
        let x = absX * this.viewWidth + 'rpx'
        let y = absY * this.viewHeight + 'rpx'
        
        this.dataList.push({
          id: this.list[i][this.idName],
          title: this.list[i][this.titleName],
          x,
          y,
          oldX: x,
          oldY: y,
          absX,
          absY,
          scale: 1,
          zIndex: 9,
          opacity: 1,
          index: this.dataList.length,
        })
      }
      this.sortList()
    })
  },
  methods: {
    onChange(e, item) {
      if(!item) return
      item.oldX = e.detail.x
      item.oldY = e.detail.y
      if (e.detail.source === 'touch') {
        let x = Math.floor((e.detail.x + this.itemWidth / 2) / this.itemWidth)
        let y = Math.floor((e.detail.y + this.itemHeight / 2) / this.itemHeight)
        let index = this.cols * y + x
        if (item.index != index && index < this.dataList.length) {
          for (let obj of this.dataList) {
            if (item.index > index && obj.index >= index && obj.index < item.index) {
              obj.index += 1
              obj.x = obj.oldX
              obj.y = obj.oldY
              obj.absX = obj.index % this.cols
              obj.absY = Math.floor(obj.index / this.cols)
              this.$nextTick(() => {
                obj.x = obj.absX * this.viewWidth + 'rpx'
                obj.y = obj.absY * this.viewHeight + 'rpx'
              })
            }
            if (item.index < index && obj.index <= index && obj.index > item.index) {
              obj.index -= 1
              obj.x = obj.oldX
              obj.y = obj.oldY
              obj.absX = obj.index % this.cols
              obj.absY = Math.floor(obj.index / this.cols)
              this.$nextTick(() => {
                obj.x = obj.absX * this.viewWidth + 'rpx'
                obj.y = obj.absY * this.viewHeight + 'rpx'
              })
            }
          }
          item.index = index
          item.absX = x
          item.absY = y
          this.sortList()
        }
      }
    },
    touchstart(item) {
      this.dataList.forEach(v => {
        v.zIndex = v.index + 10
      })
      item.zIndex = 99
      item.scale = 1.1
      item.opacity = 0.7
    },
    touchend(item) {
      item.scale = 1
      item.opacity = 1
      item.x = item.oldX
      item.y = item.oldY
      this.$nextTick(() => {
        item.x = item.absX * this.viewWidth + 'rpx'
        item.y = item.absY * this.viewHeight + 'rpx'
      })
    },
    sortList() {
      let temp = this.dataList.slice()
      temp.sort((a, b) => {
        return a.index - b.index
      })
      for (let i = 0; i < temp.length; i++) {
        for (let s of this.list) {
          if(temp[i][this.idName] == s[this.idName]){
            temp[i] = s
          }
        }
      }
      this.$emit('update:list', temp)
    },
  }
}
</script>

<style lang="scss" scoped>
.con {
  margin: 30rpx;
  .area {
    width: 100%;
    .view {
      display: flex;
      justify-content: center;
      align-items: center;
      .area-con {
        position: relative;
        border: 1rpx dashed #007AFF;
        border-radius: 10rpx;
        text-align: center;
        font-size: 28rpx;
      }
    }
    .add {
      border: 1rpx dashed rgba(0, 0, 0, 0);
      opacity: 0;
      position: absolute;
      z-index: 0;
    }
  }
}
</style>
