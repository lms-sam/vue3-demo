<template>
  <div class="cloud" :style="{ width: width + 'px', height: height + 'px' }" @mousemove="listener">
    <div class="cloud-item" v-for="cloud of clouds" :key="cloud.text" :style="{
      transform:
        `translate3d(${cloud.x}px,${cloud.y}px,${cloud.z}px) scale(${(400 + cloud.z) / 600})`,
      opacity: (400 + cloud.z) / 600,
      color: cloud.color
    }">
      {{ cloud.text }}
    </div>
  </div>
</template>

<script lang="ts" setup>
import { computed, onMounted, onUnmounted, reactive } from 'vue'

interface Cloud {
  x: number
  y: number
  z: number
  text: string
  color?: string
}

interface Tag {
  name: string
  color?: string
}

const prop = withDefaults(defineProps<{
  tags: Tag[]
  width?: string // 宽
  height?: string // 高
  radius?: string // 半径
}>(), { width: '600', height: '600', radius: '250' })

let speedX = Math.PI / 360
let speedY = Math.PI / 360
const clouds: Cloud[] = reactive([])

// 球心坐标
const CX = computed(() => { return parseInt(prop.width) / 3.5 })
const CY = computed(() => { return parseInt(prop.height) / 2 })

// 创建标签
const createTags = (): Cloud[] => {
  const clouds: Cloud[] = []
  for (let i = 0, len = prop.tags.length; i < len; i++) {
    const k = -1 + (2 * (i + 1) - 1) / len
    const a = Math.acos(k)
    const b = a * Math.sqrt(len * Math.PI)
    const cloud: Cloud = {
      x: CX.value + +prop.radius * Math.sin(a) * Math.cos(b),
      y: CY.value + +prop.radius * Math.sin(a) * Math.sin(b),
      z: +prop.radius * Math.cos(a),
      text: prop.tags[i].name,
      color: prop.tags[i].color || '#333'
    }
    clouds.push(cloud)
  }
  return clouds
}

clouds.push(...createTags())

// 云球旋转函数-X
const rotateX = (angleX: number) => {
  const cos = Math.cos(angleX)
  const sin = Math.sin(angleX)
  for (const cloud of clouds) {
    const _y = (cloud.y - CY.value) * cos - cloud.z * sin + CY.value
    const _z = cloud.z * cos + (cloud.y - CY.value) * sin
    cloud.y = _y
    cloud.z = _z
  }
}
// 云球旋转函数-Y
const rotateY = (angleY: number) => {
  const cos = Math.cos(angleY)
  const sin = Math.sin(angleY)
  for (const cloud of clouds) {
    const _x = (cloud.x - CX.value) * cos - cloud.z * sin + CX.value
    const _z = cloud.z * cos + (cloud.x - CX.value) * sin
    cloud.x = _x
    cloud.z = _z
  }
}

// 定时器
let timer: number
onMounted(() => {
  timer = setInterval(() => {
    rotateX(speedX)
    rotateY(speedY)
  }, 20)
})
onUnmounted(() => {
  clearInterval(timer)
})

// 手指滑动事件
const listener = (event: MouseEvent) => {
  const x = event.pageX - CX.value
  const y = event.pageX - CY.value
  speedX = x * 0.0001 > 0
    ? Math.min(+prop.radius * 0.00002, x * 0.0001)
    : Math.max(-prop.radius * 0.00002, x * 0.0001)
  speedY = y * 0.0001 > 0
    ? Math.min(+prop.radius * 0.00002, y * 0.0001)
    : Math.max(-prop.radius * 0.00002, y * 0.0001)
}
</script>

<style lang="scss" scoped>
.cloud {
  position: relative;

  .cloud-item {
    position: absolute;
    padding: 10px 20px;
    border-radius: 10px;
    font-size: 30px;
    transition: all 2s linear;
  }
}
</style>
