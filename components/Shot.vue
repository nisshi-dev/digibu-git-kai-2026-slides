<script setup lang="ts">
import { computed } from 'vue'

// スクショの上に、操作する場所を赤枠＋番号で重ねて表示する
// marks / crop の座標は元画像のピクセル（x1, y1, x2, y2）で指定する
// crop を指定すると、その範囲だけを切り出して表示する（映したくない部分を隠すとき用）
type Rect = [number, number, number, number]

const props = withDefaults(defineProps<{
  src: string
  alt?: string
  width?: number
  height?: number
  marks?: Rect[]
  crop?: Rect
  start?: number // 番号の開始値（スライドの手順番号に合わせる）
}>(), {
  alt: '',
  width: 2000,
  height: 1189,
  marks: () => [],
  start: 1,
})

// "/github/..." のような public/ の画像に、公開先のパス（GitHub Pages なら /リポジトリ名/）を付ける
const imgSrc = computed(() =>
  props.src.startsWith('/') ? import.meta.env.BASE_URL.replace(/\/$/, '') + props.src : props.src,
)

const area = computed<Rect>(() => props.crop ?? [0, 0, props.width, props.height])
const areaW = computed(() => area.value[2] - area.value[0])
const areaH = computed(() => area.value[3] - area.value[1])

const frameStyle = computed(() => ({ aspectRatio: `${areaW.value} / ${areaH.value}` }))

const imgStyle = computed(() => ({
  width: `${(props.width / areaW.value) * 100}%`,
  left: `${(-area.value[0] / areaW.value) * 100}%`,
  top: `${(-area.value[1] / areaH.value) * 100}%`,
}))

function boxStyle([x1, y1, x2, y2]: Rect) {
  const [ox, oy] = area.value
  return {
    left: `${((x1 - ox) / areaW.value) * 100}%`,
    top: `${((y1 - oy) / areaH.value) * 100}%`,
    width: `${((x2 - x1) / areaW.value) * 100}%`,
    height: `${((y2 - y1) / areaH.value) * 100}%`,
  }
}
</script>

<template>
  <div class="relative w-full rounded-lg overflow-hidden shadow-sm border border-[var(--rule)] bg-white" :style="frameStyle">
    <img :src="imgSrc" :alt="alt" class="absolute max-w-none" :style="imgStyle">
    <div
      v-for="(m, i) in marks"
      :key="i"
      class="absolute border-3 border-red-500 rounded-md"
      :style="boxStyle(m)"
    >
      <span
        v-if="marks.length > 1"
        class="absolute -top-3 -left-3 w-6 h-6 rounded-full bg-red-500 text-white text-xs font-bold flex items-center justify-center"
      >{{ start + i }}</span>
    </div>
  </div>
</template>
