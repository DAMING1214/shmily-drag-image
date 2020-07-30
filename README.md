# uni-app div拖拽排序插件
## shmily-drag-view
实际效果建议下载示例项目看看
[点我快速预览 Web 版](http://static-11ea0c21-6b8f-47f7-b77f-cb0c7ea3f355.bspapp.com/shmily-drag-view/)
## 使用方式：
### 在 script 中引用组件
```
import shmilyDragView from '@/components/shmily-drag-view/shmily-drag-view.vue'
export default {
  components: {
    shmilyDragView
  },
  data() {
    return {
      list: []
    }
  }
}
```
### 在 template 中使用组件
```
<shmily-drag-view :list.sync="list" :viewWidth="230" :viewHeight="100" idName="id" titleName="title"></shmily-drag-view>
```
---


属性名 | 类型 | 默认值 | 说明
:-:|:-:|:-:|---
list | Array | [ ] | 排序后数组
viewWidth | Number | 230 | 父容器宽度（实际显示的宽度为 viewWidth / 1.1 ），单位 rpx
viewHeight | Number | 100 | 父容器高度（实际显示的高度为 viewHeight / 1.2 ），单位 rpx
idName | String | id | id属性名(不可重复)
titleName | String | title | 标题属性名