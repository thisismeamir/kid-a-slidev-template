<!-- layouts/toc.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface TocEntry {
  index: string
  title: string
  page?: number     // optional explicit slide number to jump to
}

interface TocFrontmatter extends Frontmatter {
  kicker?: string
  title?: string
  affiliation?: string
  entries?: TocEntry[]
  current?: string    // index of the active/current section, if shown mid-deck
}

const { frontmatter } = defineProps<{
  frontmatter: TocFrontmatter
}>()
</script>

<template>
  <div class="slidev-layout toc relative h-full w-full m-0 p-0 bg-[var(--slate-10)]">

    <div class="h-full w-full flex flex-col justify-center px-[var(--space-7)] gap-[var(--space-1)]">
      <h3 v-if="frontmatter.title" class="text-[var(--slate-100)]">
        {{ frontmatter.title }}
      </h3>

      <ol class="flex flex-col gap-[var(--space-1)]">
        <li
          v-for="entry in frontmatter.entries"
          :key="entry.index"
          class="flex items-baseline gap-[var(--space-1)] py-[2px]
                 border-b border-[var(--slate-20)]"
          :class="entry.index === frontmatter.current
            ? 'text-[var(--slate-100)]'
            : 'text-[var(--slate-50)]'"
        >
          <span
            class="type-code w-[3ch] shrink-0"
            :class="entry.index === frontmatter.current ? 'text-[var(--orion-50)]' : 'text-[var(--slate-40)]'"
          >
            {{ entry.index }}
          </span>
          <component
            :is="entry.page ? 'a' : 'span'"
            :href="entry.page ? `#${entry.page}` : undefined"
            class="type-label flex-1"
          >
            {{ entry.title }}
          </component>
          <span v-if="entry.page" class="type-code text-[var(--slate-30)]">
            {{ String(entry.page).padStart(2, '0') }}
          </span>
        </li>
      </ol>
    </div>

    <SlideFooter author="Amir H. Ebrahimnejad" title="Echoes of Symmetry Breaking" />
  </div>
</template>
