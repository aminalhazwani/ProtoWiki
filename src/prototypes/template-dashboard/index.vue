<script setup lang="ts">
import { CdxIcon } from '@wikimedia/codex'
import { cdxIconCheck, cdxIconLinkExternal, cdxIconUserTalk } from '@wikimedia/codex-icons'

import ChromeWrapper from '@/components/ChromeWrapper.vue'
import Dashboard from '@/components/Dashboard.vue'
import DashboardModule from '@/components/DashboardModule.vue'
import SpecialPageWrapper from '@/components/SpecialPageWrapper.vue'

definePage({
  meta: {
    title: 'Template: Dashboard',
    description: 'Template for dashboard prototypes that contain "box modules".',
  },
})

const feedbackUrl = 'https://en.wikipedia.org/wiki/Wikipedia:Feedback'
const policiesUrl = 'https://en.wikipedia.org/wiki/Wikipedia:List_of_policies'

const primaryPlaceholder = 'Main column — module body content goes here.'
const sidebarPlaceholderA = 'First sidebar module — replace with your UI.'
const sidebarPlaceholderB = 'Second sidebar module — replace with your UI.'
</script>

<template>
  <ChromeWrapper :last-edited-notice="false">
    <SpecialPageWrapper title="Dashboard" help>
      <div class="template-dashboard-shell">
        <Dashboard>
          <template #banner>
            <a
              :href="feedbackUrl"
              target="_blank"
              rel="noopener noreferrer"
              class="dashboard-mobile-banner__feedback"
            >
              Share feedback
              <CdxIcon :icon="cdxIconLinkExternal" size="x-small" />
            </a>
          </template>

          <template #mobile>
            <DashboardModule
              class="dashboard-slot--mobile-primary"
              to="/chrome-template"
              title="Thank collaborators"
              cta-label="Open module"
            >
              <p class="dashboard-template-placeholder">No recent edits in scope.</p>
            </DashboardModule>

            <DashboardModule class="dashboard-slot--mobile-sidebar" title="Your impact">
              <div class="dashboard-impact-rows">
                <div class="dashboard-impact-row">
                  <CdxIcon :icon="cdxIconUserTalk" size="small" class="dashboard-impact-icon" />
                  <span class="dashboard-impact-metric">—</span>
                  <span>Thanks sent.</span>
                </div>
                <div class="dashboard-impact-row">
                  <CdxIcon :icon="cdxIconCheck" size="small" class="dashboard-impact-icon" />
                  <span class="dashboard-impact-metric">—</span>
                  <span>Edits completed.</span>
                </div>
              </div>
            </DashboardModule>

            <DashboardModule
              :href="policiesUrl"
              title="Policies (example)"
              cta-label="View on Wikipedia"
            >
              <p class="dashboard-template-placeholder">
                Opens Wikipedia in a new tab — replace with your module content.
              </p>
            </DashboardModule>
          </template>

          <template #primary>
            <DashboardModule title="Primary module slot">
              <p class="dashboard-template-placeholder">{{ primaryPlaceholder }}</p>
            </DashboardModule>
          </template>

          <template #sidebar>
            <DashboardModule class="dashboard-slot--desktop-sidebar" title="Sidebar module slot">
              <p class="dashboard-template-placeholder">{{ sidebarPlaceholderA }}</p>
            </DashboardModule>
            <DashboardModule
              class="dashboard-slot--desktop-secondary"
              title="Secondary module slot"
            >
              <p class="dashboard-template-placeholder">{{ sidebarPlaceholderB }}</p>
            </DashboardModule>
          </template>
        </Dashboard>
      </div>
    </SpecialPageWrapper>
  </ChromeWrapper>
</template>

<style scoped>
.template-dashboard-shell {
  box-sizing: border-box;
  width: 100%;
  min-width: 0;
}

.dashboard-template-placeholder {
  margin: 0;
  font-size: 14px;
  line-height: 1.4;
  color: var(--color-base--subtle, #54595d);
}

.dashboard-impact-rows {
  display: flex;
  flex-direction: column;
  gap: var(--spacing-50, 8px);
  font-size: 14px;
  line-height: 1.4;
  color: var(--color-base, #202122);
}

.dashboard-impact-row {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  column-gap: var(--spacing-50, 8px);
  row-gap: var(--spacing-25, 4px);
}

.dashboard-impact-icon {
  flex-shrink: 0;
  color: var(--color-base--subtle, #54595d);
}

.dashboard-impact-metric {
  color: var(--color-progressive, #36c);
  font-weight: var(--font-weight-bold, 700);
  min-width: 1.25em;
}

:deep(.dashboard-slot--mobile-primary .dashboard-module__body) {
  min-height: 3rem;
}

:deep(.dashboard-slot--desktop-primary .dashboard-module) {
  min-height: 8rem;
}
</style>
