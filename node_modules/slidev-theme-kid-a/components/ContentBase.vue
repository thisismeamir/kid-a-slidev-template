<!-- layouts/content-base.vue -->
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
  <div class="slidev-layout content-base relative h-full w-full m-0 p-4 bg-[var(--slate-10)] ">
    <SlideHeader
      :index="frontmatter.index"
      :section="frontmatter.kicker"
      :affiliation="frontmatter.affiliation"
    />

    <div class="flex h-full w-full pt-[var(--space-7)] pb-[var(--space-6)]">
      <!-- Primary: 70%, fixed regardless of aside content -->
      <div class="flex flex-col gap-[var(--space-3)] px-[var(--space-2)] w-[70%]">
        <h3 v-if="frontmatter.title" class="text-[var(--slate-100)]">
          {{ frontmatter.title }}
        </h3>
        <div class="flex-1 min-h-0 scale-[0.9] p-0 pt-1">
          <slot />
        </div>
      </div>

      <!-- Aside: 30%, always reserved as a line-length break, empty is valid -->
      <div
        class="w-[30%] flex flex-col justify-start gap-[var(--space-2)]
               px-[var(--space-4)] border-l border-[var(--slate-20)]"
      >
        <slot name="aside" />
      </div>
    </div>

    <SlideFooter :title="frontmatter.title" />
  </div>
</template>
