<!-- layouts/content-quote.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface QuoteFrontmatter extends Frontmatter {
  index?: string
  kicker?: string
  affiliation?: string
  reverse?: boolean    // flips flex-direction: quote renders first when true
  ratio?: string        // "figure/quote", e.g. "40/60" — defaults 40/60
  quote?: string
  attribution?: string  // e.g. "V. Domcke, CERN-TH"
  context?: string      // e.g. talk/paper the quote is drawn from
}

const { frontmatter } = defineProps<{
  frontmatter: QuoteFrontmatter
}>()

const figureWidth = frontmatter.ratio?.split('/')[0]?.trim() ?? '40'
const quoteWidth = frontmatter.ratio?.split('/')[1]?.trim() ?? '60'
</script>

<template>
  <div class="slidev-layout content-quote relative h-full w-full m-0 p-0 bg-[var(--slate-10)]">
    <SlideHeader
      :index="frontmatter.index"
      :section="frontmatter.kicker"
      :affiliation="frontmatter.affiliation"
    />

    <div
      class="flex h-full w-full pt-[var(--space-7)] pb-[var(--space-6)] gap-[var(--space-4)]"
      :class="frontmatter.reverse ? 'flex-row-reverse' : 'flex-row'"
    >
      <!-- Figure column -->
      <div
        class="flex items-center justify-center min-w-0 px-[var(--space-6)]"
        :style="{ width: `${figureWidth}%` }"
      >
        <slot name="figure" />
      </div>

      <!-- Quote column -->
      <div
        class="flex flex-col justify-center gap-[var(--space-3)] min-w-0
               border-[var(--slate-20)] px-[var(--space-6)]"
        :class="frontmatter.reverse ? 'border-r' : 'border-l'"
        :style="{ width: `${quoteWidth}%` }"
      >
        <p class="text-[var(--text-6xl)] leading-[var(--leading-6xl)] font-[var(--weight-light)] text-[var(--slate-100)]">
          <span class="text-[var(--orion-40)]">“</span><slot>{{ frontmatter.quote }}</slot><span class="text-[var(--orion-40)]">”</span>
        </p>
        <div v-if="frontmatter.attribution || frontmatter.context" class="space-y-[var(--space-1)]">
          <p v-if="frontmatter.attribution" class="type-label text-[var(--slate-60)]">
            {{ frontmatter.attribution }}
          </p>
          <p v-if="frontmatter.context" class="type-code text-[var(--slate-40)]">
            {{ frontmatter.context }}
          </p>
        </div>
      </div>
    </div>

    <SlideFooter />
  </div>
</template>
