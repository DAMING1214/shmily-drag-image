<template>
	<view>
		<shmily-drag-view :list.sync="list" :viewWidth="229" :viewHeight="100" idName="id" titleName="title"></shmily-drag-view>
	</view>
</template>

<script>
  import shmilyDragView from '@/components/shmily-drag-view/shmily-drag-view.vue'
	export default {
    components:{
      shmilyDragView
    },
		data() {
			return {
        // 排序后的列表
				list: [
          {
            id: this.guid(),
            title: '人事管理学1'
          },
          {
            id: this.guid(),
            title: '人事管理学2'
          },
          {
            id: this.guid(),
            title: '人事管理学3'
          },
          {
            id: this.guid(),
            title: '人事管理学4'
          },
          {
            id: this.guid(),
            title: '人事管理学5'
          },
          {
            id: this.guid(),
            title: '人事管理学6'
          }
        ]
			}
		},
    methods: {
      guid() {
      	function S4() {
      		return (((1 + Math.random()) * 0x10000) | 0).toString(16).substring(1);
      	}
      	return (S4() + S4() + "-" + S4() + "-" + S4() + "-" + S4() + "-" + S4() + S4() + S4());
      }
    }
	}
</script>

<style>
	
</style>
