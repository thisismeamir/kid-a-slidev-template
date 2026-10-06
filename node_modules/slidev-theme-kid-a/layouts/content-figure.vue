<!-- layouts/content-figure.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface FigureFrontmatter extends Frontmatter {
  index?: string
  kicker?: string
  title?: string
  affiliation?: string
  reverse?: boolean   // flips flex-direction: text renders first when true
  ratio?: string       // "figure/text", e.g. "60/40" — defaults 60/40
  caption?: string     // short caption under the figure
  source?: string      // e.g. "PandoraPFA reconstruction, 2026"
}

const { frontmatter } = defineProps<{
  frontmatter: FigureFrontmatter
}>()

const figureWidth = frontmatter.ratio?.split('/')[0]?.trim() ?? '60'
const textWidth = frontmatter.ratio?.split('/')[1]?.trim() ?? '40'
</script>

<template>
  <ContentBase :frontmatter="frontmatter">
    <div
      class="flex h-full gap-[var(--space-2)] text-[9pt]"
      :class="frontmatter.reverse ? 'flex-row-reverse' : 'flex-row'"
    >
      <!-- Figure column -->
      <div
        class="flex flex-col gap-[var(--space-4)] w-full"
        :style="{ width: `${figureWidth}%` }"
      >
        <div class="flex-1 min-h-0 flex items-center justify-center w-full h-full *:h-full *:w-full">
          <slot name="figure" />
        </div>
        <div
          v-if="frontmatter.caption || frontmatter.source"
          class="flex items-baseline justify-between gap-[var(--space-2)] pt-[var(--space-1)] border-t border-[var(--slate-20)]"
        >
          <p v-if="frontmatter.caption" class="type-label text-[var(--slate-60)]">
            {{ frontmatter.caption }}
          </p>
          <p v-if="frontmatter.source" class="type-code text-[var(--slate-40)] whitespace-nowrap">
            {{ frontmatter.source }}
          </p>
        </div>
      </div>

      <!-- Text column -->
      <div
        class="flex flex-col  min-w-0
               border-[var(--slate-20)] "
        :class="frontmatter.reverse ? 'border-r pr-[var(--space-1)]' : 'border-l pl-[var(--space-1)]'"

      >
        <slot name="text" />
      </div>
    </div>

    <template #aside>
      <slot name="aside" />
    </template>
  </ContentBase>
</template>
