<script setup lang="ts">
import type { RouteLocationRaw } from 'vue-router'
import { CdxIcon } from '@wikimedia/codex'
import { cdxIconUserAnonymous } from '@wikimedia/codex-icons'

import DashboardModule from '@/components/DashboardModule.vue'

interface Props {
  mentorName: string
  lastActiveDaysAgo: number
  editCount?: number
  mentorNote?: string
  to?: RouteLocationRaw
  learnMoreHref?: string
  conversationsHref?: string
}

const props = withDefaults(defineProps<Props>(), {
  editCount: undefined,
  mentorNote: undefined,
  to: undefined,
  learnMoreHref: undefined,
  conversationsHref: undefined,
})
</script>

<template>
  <DashboardModule title="Your mentor" :to="props.to" hide-cta>
    <!-- Mobile: simplified user row inside link card -->
    <template v-if="props.to != null">
      <p class="mentor-module__intro">
        We've assigned you an experienced editor to answer your questions about editing.
      </p>
      <div class="mentor-module__user">
        <CdxIcon :icon="cdxIconUserAnonymous" size="medium" class="mentor-module__avatar" />
        <div class="mentor-module__user-info">
          <span class="mentor-module__name">{{ props.mentorName }}</span>
          <span class="mentor-module__meta">Active {{ props.lastActiveDaysAgo }} days ago</span>
        </div>
      </div>
    </template>

    <!-- Desktop: full detail inside static sidebar card -->
    <template v-else>
      <p class="mentor-module__intro">
        We've assigned you an experienced editor to answer your questions about editing.
        <a
          v-if="props.learnMoreHref"
          :href="props.learnMoreHref"
          class="mentor-module__link"
        >Learn more about mentors.</a>
      </p>
      <div class="mentor-module__user">
        <CdxIcon :icon="cdxIconUserAnonymous" size="medium" class="mentor-module__avatar" />
        <div class="mentor-module__user-info">
          <span class="mentor-module__name mentor-module__name--progressive">{{ props.mentorName }}</span>
          <span class="mentor-module__meta">
            <span v-if="props.editCount != null">{{ props.editCount.toLocaleString() }} edits · </span>Active {{ props.lastActiveDaysAgo }} days ago
          </span>
        </div>
      </div>
      <blockquote v-if="props.mentorNote" class="mentor-module__note">
        {{ props.mentorNote }}
      </blockquote>
      <button class="mentor-module__ask-btn">Ask your mentor a question about editing</button>
      <a
        v-if="props.conversationsHref"
        :href="props.conversationsHref"
        class="mentor-module__link mentor-module__conversations"
      >View your mentor's other conversations</a>
    </template>
  </DashboardModule>
</template>

<style scoped>
.mentor-module__intro {
  margin: 0 0 0.75rem;
  font-size: 14px;
  line-height: 1.4;
  color: var(--color-base, #202122);
}

.mentor-module__user {
  display: flex;
  align-items: center;
  gap: var(--spacing-75, 12px);
}

.mentor-module__avatar {
  flex-shrink: 0;
  color: var(--color-base--subtle, #54595d);
  width: 2.5rem;
  height: 2.5rem;
}

.mentor-module__user-info {
  display: flex;
  flex-direction: column;
  gap: 2px;
  min-width: 0;
}

.mentor-module__name {
  font-size: 14px;
  font-weight: var(--font-weight-bold, 700);
  color: var(--color-base, #202122);
}

.mentor-module__name--progressive {
  color: var(--color-progressive, #36c);
}

.mentor-module__meta {
  font-size: 13px;
  color: var(--color-base--subtle, #54595d);
}

.mentor-module__note {
  margin: 0.75rem 0 0;
  padding: 0.25rem 0 0.25rem 0.75rem;
  border-left: 4px solid var(--border-color-subtle, #a2a9b1);
  font-size: 14px;
  line-height: 1.4;
  color: var(--color-base, #202122);
}

.mentor-module__ask-btn {
  display: block;
  width: 100%;
  margin-top: 0.75rem;
  padding: 0.4rem 1rem;
  background-color: transparent;
  color: var(--color-base, #202122);
  font-size: 14px;
  font-weight: var(--font-weight-bold, 700);
  text-align: center;
  border: 1px solid var(--border-color-base, #a2a9b1);
  border-radius: 2px;
  cursor: pointer;
  box-sizing: border-box;
}

.mentor-module__ask-btn:hover {
  background-color: var(--background-color-interactive, #eaecf0);
}

.mentor-module__link {
  color: var(--color-progressive, #36c);
  text-decoration: none;
}

.mentor-module__link:hover {
  text-decoration: underline;
}

.mentor-module__conversations {
  display: block;
  margin-top: 0.5rem;
  font-size: 14px;
}
</style>
