<!-- layouts/section.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface SectionFrontmatter extends Frontmatter {
  index?: string       // e.g. "02"
  kicker?: string       // e.g. "SECTION"
  title?: string
  subtitle?: string
  agenda?: string[]     // optional list of what this section covers
}

const { frontmatter } = defineProps<{
  frontmatter: SectionFrontmatter
}>()
</script>

<template>
  <div class="slidev-layout section flex flex-col h-full w-full m-0 p-0 bg-[var(--slate-10)]">
    <div
      class="flex-1 flex flex-col justify-between
             px-[var(--space-6)] py-[var(--space-6)]"
    >
      <Kicker v-if="frontmatter.kicker" :index="frontmatter.index" :label="frontmatter.kicker" />
      <span v-else />

      <div class="flex items-end justify-between gap-[var(--space-5)]">
        <div class="space-y-[var(--space-2)] max-w-[70%]">
          <h2 class="text-[var(--slate-100)]">
            <slot name="title">{{ frontmatter.title }}</slot>
          </h2>
          <p
            v-if="frontmatter.subtitle"
            class="type-label text-[var(--slate-60)]"
          >
            {{ frontmatter.subtitle }}
          </p>
        </div>

        <span
          v-if="frontmatter.index"
          class="type-code text-[var(--orion-30)] text-[var(--text-13xl)] leading-none"
        >
          {{ frontmatter.index }}
        </span>
      </div>

      <ul
        v-if="frontmatter.agenda?.length"
        class="flex flex-col gap-[var(--space-1)] border-t border-[var(--slate-20)] pt-[var(--space-3)]"
      >
        <li
          v-for="(item, i) in frontmatter.agenda"
          :key="i"
          class="flex items-baseline gap-[var(--space-2)] type-label text-[var(--slate-50)]"
        >
          <span class="type-code text-[var(--orion-50)]">
            {{ String(i + 1).padStart(2, '0') }}
          </span>
          {{ item }}
        </li>
      </ul>
    </div>
  </div>
</template>
