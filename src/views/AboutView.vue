<template>
  <div class="about">
    <h1>This is an about page</h1>
    <tag-cloud :tags="tags" width="600" height="600" radius="250"></tag-cloud>
  </div>
</template>

<script lang="ts" setup>
// import { Options, Vue } from 'vue-class-component'
import TagCloud from '@/components/TagCloud.vue' // @ is an alias to /src

// Options({
//   components: {
//     TagCloud
//   }
// })

const tags = [
  { name: '01. 人员招聘难', color: '#006666' },
  { name: '02. 人才复制难', color: '#009999' },
  { name: '03. 门店管理难', color: '#333333' },
  { name: '04. 客户引流难', color: '#660099' },
  { name: '05. 客户不稳定', color: '#990066' },
  { name: '06. 技术门槛高', color: '#CC0099' },
  { name: '07. 营销策划能力弱', color: '#CC3300' },
  { name: '08. 行业内卷利润低', color: '#FF9900' },
  { name: '09. 产品严重同质化', color: '#006633' },
  { name: '10. 技术项目被浮夸' } // color 可以不传，默认黑色
]
// export default class HomeView extends Vue {}
</script>
