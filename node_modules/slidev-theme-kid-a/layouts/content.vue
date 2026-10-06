<!-- layouts/content.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface ContentFrontmatter extends Frontmatter {
  index?: string
  kicker?: string
  title?: string
  affiliation?: string
}

const { frontmatter } = defineProps<{
  frontmatter: ContentFrontmatter
}>()
</script>

<template>
  <ContentBase :frontmatter="frontmatter">
    <div class="flex flex-col gap-[var(--space-2)] type-body text-[var(--slate-90)] text-[9pt]">
      <slot />
    </div>

    <template #aside>
      <slot name="aside" />
    </template>
  </ContentBase>
</template>
