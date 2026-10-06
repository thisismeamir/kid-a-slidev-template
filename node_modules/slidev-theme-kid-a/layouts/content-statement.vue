<!-- layouts/content-statement.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface StatementFrontmatter extends Frontmatter {
  index?: string
  kicker?: string
  affiliation?: string
  statement?: string   // the claim/result itself
  support?: string     // small supporting line — units, dataset, p-value, etc.
  source?: string       // e.g. citation or dataset reference
}

const { frontmatter } = defineProps<{
  frontmatter: StatementFrontmatter
}>()
</script>

<template>
  <div class="slidev-layout content-statement relative h-full w-full m-0 p-0 bg-[var(--slate-10)]">
    <SlideHeader
      :index="frontmatter.index"
      :section="frontmatter.kicker"
      :affiliation="frontmatter.affiliation"
    />

    <div class="h-full w-full flex flex-col items-start justify-center px-[var(--space-7)] gap-[var(--space-1)]">
      <p class="text-[var(--text-13xl)] leading-[var(--leading-13xl)] font-[var(--weight-light)] text-[var(--slate-100)] max-w-[85%]">
        <slot>{{ frontmatter.statement }}</slot>
      </p>
      <p
        v-if="frontmatter.support"
        class="type-label text-[var(--slate-60)] max-w-[70%]"
      >
        {{ frontmatter.support }}
      </p>
    </div>

    <div
      v-if="frontmatter.source"
      class="absolute bottom-[var(--space-4)] left-[var(--space-7)] type-code text-[var(--slate-40)]"
    >
      {{ frontmatter.source }}
    </div>

    <SlideFooter />
  </div>
</template>
