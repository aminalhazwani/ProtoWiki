<script setup lang="ts">
import { computed } from 'vue'
import type { RouteLocationRaw } from 'vue-router'

import DashboardModule from '@/components/DashboardModule.vue'

interface Props {
  to?: RouteLocationRaw
  /** Formatted total views, e.g. "10.8K" */
  viewCount?: string
  viewLabel?: string
  /** Raw data points for the sparkline */
  sparklineData?: number[]
  lastEdited?: string
  longestStreak?: string
}

const props = withDefaults(defineProps<Props>(), {
  to: undefined,
  viewCount: undefined,
  viewLabel: 'Views on articles you\'ve edited',
  sparklineData: () => [],
  lastEdited: undefined,
  longestStreak: undefined,
})

const W = 300
const H = 48

function toPoints(data: number[]): [number, number][] {
  const min = Math.min(...data)
  const max = Math.max(...data)
  const range = max - min || 1
  return data.map((v, i) => [
    (i / (data.length - 1)) * W,
    H - ((v - min) / range) * (H * 0.8) - H * 0.1,
  ])
}

const linePath = computed(() => {
  const pts = props.sparklineData
  if (pts.length < 2) return ''
  return toPoints(pts)
    .map(([x, y], i) => `${i === 0 ? 'M' : 'L'}${x.toFixed(1)},${y.toFixed(1)}`)
    .join(' ')
})

const areaPath = computed(() => {
  if (!linePath.value) return ''
  return `${linePath.value} L${W},${H} L0,${H} Z`
})

const hasContent = computed(() => !!props.viewCount)
</script>

<template>
  <DashboardModule title="Your impact" :to="to" hide-cta>
    <template v-if="hasContent">
      <div class="impact-module__stat-row">
        <span class="impact-module__count">{{ viewCount }}</span>
        <span class="impact-module__count-label">{{ viewLabel }}</span>
      </div>

      <svg
        v-if="sparklineData.length >= 2"
        class="impact-module__sparkline"
        :viewBox="`0 0 ${W} ${H}`"
        preserveAspectRatio="none"
        aria-hidden="true"
      >
        <path :d="areaPath" class="impact-module__area" />
        <path :d="linePath" class="impact-module__line" />
      </svg>

      <div v-if="lastEdited || longestStreak" class="impact-module__metrics">
        <div v-if="lastEdited" class="impact-module__metric">
          <span class="impact-module__metric-label">Last edited</span>
          <span class="impact-module__metric-value">{{ lastEdited }}</span>
        </div>
        <div v-if="longestStreak" class="impact-module__metric">
          <span class="impact-module__metric-label">Longest streak</span>
          <span class="impact-module__metric-value">{{ longestStreak }}</span>
        </div>
      </div>
    </template>
  </DashboardModule>
</template>

<style scoped>
.impact-module__stat-row {
  display: flex;
  align-items: baseline;
  gap: var(--spacing-50, 8px);
  flex-wrap: nowrap;
  margin-bottom: var(--spacing-25, 4px);
}

.impact-module__count {
  font-size: var(--font-size-xx-large, 2rem);
  font-weight: var(--font-weight-bold, 700);
  line-height: 1;
}

.impact-module__count-label {
  font-size: var(--font-size-medium);
  line-height: var(--line-height-medium);
}

.impact-module__sparkline {
  display: block;
  width: var(--size-full);
  height: var(--size-100);
  overflow: visible;
  margin-bottom: var(--spacing-50, 8px);
}

.impact-module__area {
  fill: var(--background-color-progressive-subtle);
  stroke: none;
}

.impact-module__line {
  fill: none;
  stroke: var(--border-color-progressive);
  stroke-width: 1.5;
  stroke-linejoin: round;
  stroke-linecap: round;
}

.impact-module__metrics {
  display: flex;
  gap: var(--spacing-100, 16px);
  font-size: var(--font-size-medium);
  line-height: var(--line-height-medium);
}

.impact-module__metric {
  display: flex;
  flex-direction: column;
  flex-grow: 1;
}

.impact-module__metric-value {
  font-weight: var(--font-weight-bold, 700);
}
</style>
