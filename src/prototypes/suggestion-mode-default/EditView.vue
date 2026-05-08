<script setup lang="ts">
  import { ref, computed } from 'vue'
  import { CdxButton, CdxIcon } from '@wikimedia/codex'
  import { cdxIconClose, cdxIconEdit, cdxIconEllipsis, cdxIconSuccess, cdxIconUndo } from '@wikimedia/codex-icons'
  import type { CardData } from './types'

  const props = defineProps<{ cards: CardData[] }>()
  const emit = defineEmits<{ close: [] }>()

  const removedIndices = ref(new Set<number>())
  const anyRemoved = computed(() => removedIndices.value.size > 0)

  function onRemoveLink(i: number) {
    removedIndices.value = new Set([...removedIndices.value, i])
  }

  function onUndoRemove(i: number) {
    const next = new Set(removedIndices.value)
    next.delete(i)
    removedIndices.value = next
  }


  function resolvePreviewHTML(html: string): string {
    const div = document.createElement('div')
    div.innerHTML = html
    div.querySelectorAll('a.card__preview-duplicate').forEach(a => {
      a.replaceWith(document.createTextNode(a.textContent ?? ''))
    })
    div.querySelectorAll('.card__preview-duplicate').forEach(el => {
      el.classList.remove('card__preview-duplicate')
    })
    return div.innerHTML
  }

  function titleFor(type: CardData['type']): string {
    return {
      'remove-duplicate': 'Remove duplicate link',
      'add-citation': 'Add a citation',
      'ai-content': 'Potential AI-generated content',
    }[type]
  }

  function descriptionFor(type: CardData['type']): string {
    return {
      'remove-duplicate': 'This link appears more than once in this section. Help readers navigate more easily by removing <a href="#">repeated links</a>.',
      'add-citation': 'Help readers understand where this information is coming from by adding a citation.',
      'ai-content': 'This text may include <a href="#">AI-generated content</a>. Help readers trust the article by removing any AI content or rewriting any inaccurate, unverifiable, or unencyclopedic information.',
    }[type]
  }

  function actionsFor(type: CardData['type']): { label: string }[] {
    return {
      'remove-duplicate': [{ label: 'Remove link' }, { label: 'Dismiss' }],
      'add-citation': [{ label: 'Add citation' }, { label: 'No' }],
      'ai-content': [{ label: 'Edit' }, { label: 'Dismiss' }],
    }[type]
  }
</script>

<template>
  <div class="edit-view">
    <header class="edit-view__header">
      <h3 class="edit-view__title">Edit: Alan Kay</h3>
      <CdxButton weight="quiet" aria-label="Close" @click="emit('close')">
        <CdxIcon :icon="cdxIconClose" />
      </CdxButton>
    </header>
    <p class="edit-view__suggestion-count">{{ cards.length }} edit suggestions</p>
    <div class="edit-view__body">
      <div class="edit-view__carousel">
        <div v-for="(card, i) in cards" :key="i" class="edit-view__card">
          <div
            class="card__preview"
            v-html="removedIndices.has(i) ? resolvePreviewHTML(card.previewHTML) : card.previewHTML"
          />
          <div class="card__bottom">
            <Transition name="card-confirm" mode="out-in">
              <div v-if="!removedIndices.has(i)" key="instructions" class="card__instructions">
                <div class="card__instructions-header">
                  <p class="card__instructions-title">{{ titleFor(card.type) }}</p>
                </div>
                <!-- eslint-disable-next-line vue/no-v-html -->
                <p class="card__instructions-description" v-html="descriptionFor(card.type)" />
                <div class="card__actions">
                  <template v-for="action in actionsFor(card.type)" :key="action.label">
                    <CdxButton
                      v-if="card.type === 'remove-duplicate' && action.label === 'Remove link'"
                      @click="onRemoveLink(i)"
                    >
                      {{ action.label }}
                    </CdxButton>
                    <CdxButton v-else>{{ action.label }}</CdxButton>
                  </template>
                  <CdxButton weight="quiet" aria-label="More options" class="card__actions-more">
                    <CdxIcon :icon="cdxIconEllipsis" />
                  </CdxButton>
                </div>
              </div>
              <div v-else key="message" class="card__message">
                <CdxIcon :icon="cdxIconSuccess" class="card__message-icon" />
                <span class="card__message-label">Remove duplicate link</span>
                <CdxButton weight="quiet" size="small" class="card__message-undo" aria-label="Undo" @click="onUndoRemove(i)">
                  <CdxIcon :icon="cdxIconUndo" />
                </CdxButton>
              </div>
            </Transition>
          </div>
        </div>
      </div>
    </div>
    <footer class="edit-view__footer">
      <Transition name="card-confirm">
        <CdxButton
          v-if="anyRemoved"
          key="publish"
          action="progressive"
          weight="primary"
          size="large"
          class="edit-view__publish-button"
        >
          Publish
        </CdxButton>
      </Transition>
      <CdxButton weight="quiet" size="large">
        <CdxIcon :icon="cdxIconEdit" />
        Edit full article
      </CdxButton>
    </footer>
  </div>
</template>

<style scoped>
  .edit-view {
    position: fixed;
    inset: 0;
    z-index: 200;
    display: flex;
    flex-direction: column;
    background-color: var(--background-color-neutral-subtle, #f8f9fa);
  }

  .edit-view__header {
    display: flex;
    align-items: center;
    padding: var(--spacing-100, 16px);
    position: relative;
  }

  .edit-view__title {
    flex: 1;
    margin: 0;
    font-family: var(--font-family-system-sans);
    text-align: center;
  }

  .edit-view__header > button {
    position: absolute;
    right: var(--spacing-100);
  }

  .edit-view__suggestion-count {
    text-align: center;
    font-weight: bold;
    color: var(--color-success);
    font-size: var(--font-size-small);
    line-height: var(--line-height-small);
  }

  .edit-view__body {
    flex: 1;
    display: flex;
    overflow: hidden;
  }

  .edit-view__carousel {
    flex: 1;
    display: flex;
    align-items: stretch;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    gap: var(--spacing-100, 16px);
    padding-inline: var(--spacing-200, 32px);
    scroll-padding-inline: var(--spacing-200, 32px);
    -webkit-overflow-scrolling: touch;
    scrollbar-width: none;
  }

  .edit-view__carousel::-webkit-scrollbar {
    display: none;
  }

  .edit-view__card {
    flex-shrink: 0;
    width: 100%;
    scroll-snap-align: center;
    background-color: var(--background-color-base, #fff);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  .card__preview {
    flex: 1;
    min-height: 0;
    overflow-y: auto;
    padding: var(--spacing-100, 16px);
    font-size: var(--font-size-medium, 1rem);
    line-height: var(--line-height-medium, 1.6);
  }

  .card__preview :deep(a) {
    color: var(--color-progressive, #3366cc);
    text-decoration: none;
  }

  .card__preview :deep(.card__preview-duplicate) {
    background-color: var(--background-color-warning-subtle, #fef6e7);
  }

  .card__preview :deep(blockquote) {
    border-left: 3px solid var(--border-color-base, #a2a9b1);
    margin: var(--spacing-100, 16px) 0 0 var(--spacing-150, 24px);
    padding-left: var(--spacing-150, 24px);
  }

  .card__preview :deep(h2),
  .card__preview :deep(h3) {
    font-size: 1.5rem;
    border-bottom: 1px solid var(--border-color-subtle, #c8ccd1);
    padding-bottom: var(--spacing-75, 6px);
    margin: 0 0 var(--spacing-100, 16px);
  }

  .card__bottom {
    flex-shrink: 0;
    overflow: hidden;
  }

  .card__instructions {
    padding: var(--spacing-75, 12px) var(--spacing-100, 16px) var(--spacing-100, 16px);
    background-color: var(--background-color-neutral);
  }

  .card__instructions-title {
    margin: 0 0 var(--spacing-50, 8px);
    font-weight: var(--font-weight-bold, 700);
  }

  .card__instructions-description {
    margin: 0 0 var(--spacing-75, 12px);
    color: var(--color-base, #202122);
    line-height: var(--line-height-small);
  }

  .card__instructions-description :deep(a) {
    color: var(--color-progressive, #3366cc);
    text-decoration: none;
  }

  .card__actions {
    display: flex;
    align-items: center;
    gap: var(--spacing-75, 12px);
  }

  .card__actions-more {
    margin-inline-start: auto;
  }

  .card__instructions-header {
    margin-bottom: var(--spacing-25, 2px);
  }

  .card__instructions-header .card__instructions-title {
    margin: 0;
  }

  .card__message {
    display: flex;
    align-items: center;
    gap: var(--spacing-50, 8px);
    padding: var(--spacing-100, 16px);
    background-color: var(--background-color-success-subtle);
    color: var(--color-success);
  }

  .card__message-icon {
    color: var(--color-icon-success);
  }

  .card__message-label {
    font-weight: var(--font-weight-bold, 700);
    flex: 1;
  }

  .card__message-undo {
    color: var(--color-neutral);
    margin-inline-start: auto;
  }

  .card-confirm-enter-active {
    transition: transform 320ms cubic-bezier(0.32, 0.72, 0, 1);
  }

  .card-confirm-leave-active {
    transition: transform 320ms cubic-bezier(0.23, 1, 0.32, 1);
  }

  .card-confirm-enter-from,
  .card-confirm-leave-to {
    transform: translateY(100%);
  }

  @media (prefers-reduced-motion: reduce) {
    .card-confirm-enter-active,
    .card-confirm-leave-active {
      transition: opacity 200ms ease;
      transform: none;
    }

    .card-confirm-enter-from,
    .card-confirm-leave-to {
      opacity: 0;
    }
  }

  .edit-view__footer {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: var(--spacing-75, 12px);
    padding: var(--spacing-200, 32px);
  }

  .edit-view__publish-button {
    width: 100%;
  }
</style>
