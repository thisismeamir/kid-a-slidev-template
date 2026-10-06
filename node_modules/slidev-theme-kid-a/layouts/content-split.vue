<!-- layouts/content-split.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface SplitFrontmatter extends Frontmatter {
  index?: string
  kicker?: string
  title?: string
  affiliation?: string
  reverse?: boolean   // flips flex-direction: right slot renders first
  ratio?: string      // e.g. "50/50", "60/40" — defaults 50/50
}

const { frontmatter } = defineProps<{
  frontmatter: SplitFrontmatter
}>()

const leftWidth = frontmatter.ratio?.split('/')[0]?.trim() ?? '50'
const rightWidth = frontmatter.ratio?.split('/')[1]?.trim() ?? '50'
</script>

<template>
  <ContentBase :frontmatter="frontmatter">
    <div
      class="flex h-full gap-[var(--space-1)] scale-[0.9]"
      :class="frontmatter.reverse ? 'flex-row-reverse' : 'flex-row'"
    >
      <div
        class="flex flex-col gap-[var(--space-2)] min-w-0"
        :style="{ width: `${leftWidth}%` }"
      >
        <slot name="left" />
      </div>
      <div
        class="flex flex-col gap-[var(--space-1)] min-w-0
               border-[var(--slate-20)]"
        :class="frontmatter.reverse ? 'border-r pr-[var(--space-2)]' : 'border-l pl-[var(--space-2)]'"
        :style="{ width: `${rightWidth}%` }"
      >
        <slot name="right" />
      </div>
    </div>

    <template #aside>
      <slot name="aside" />
    </template>
  </ContentBase>
</template>
