<script setup lang="ts">
import type { RouteLocationRaw } from 'vue-router'

import DashboardModule from '@/components/DashboardModule.vue'

interface Props {
  to?: RouteLocationRaw
}

withDefaults(defineProps<Props>(), {
  to: undefined,
})
</script>

<template>
  <DashboardModule title="Your impact" :to="to" hide-cta />
</template>
