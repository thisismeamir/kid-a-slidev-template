<!-- layouts/cover.vue -->
<script setup lang="ts">
import type { Frontmatter } from "@slidev/types";

interface CoverFrontmatter extends Frontmatter {
  index?: string;
  kicker?: string;
  title?: string;
  subtitle?: string;
  author?: string;
  affiliation?: string;
  date?: string;
}

const { frontmatter } = defineProps<{
  frontmatter: CoverFrontmatter;
}>();
</script>

<template>
  <div
    class="slidev-layout cover grid grid-cols-2 h-full w-full m-0 p-0 bg-[var(--slate-10)]"
  >
    <!-- Info side -->
    <div
      class="flex flex-col justify-between px-[var(--space-6)] py-[var(--space-6)] border-r border-[var(--slate-20)]"
    >
      <Kicker
        v-if="frontmatter.kicker"
        :index="frontmatter.index"
        :label="frontmatter.kicker"
      />
      <span v-else />

      <div class="space-y-[var(--space-3)]">
        <h1 class="text-[var(--slate-100)] max-w-[90%]">
          <slot name="title">{{ frontmatter.title }}</slot>
        </h1>
        <p
          v-if="frontmatter.subtitle"
          class="type-label text-[var(--slate-60)] max-w-[85%]"
        >
          {{ frontmatter.subtitle }}
        </p>
      </div>

      <div class="flex items-end justify-between">
        <div class="space-y-[var(--space-1)]">
          <p
            v-if="frontmatter.author"
            class="type-label text-[var(--slate-90)]"
          >
            {{ frontmatter.author }}
          </p>
          <p
            v-if="frontmatter.affiliation"
            class="type-code text-[var(--slate-50)]"
          >
            {{ frontmatter.affiliation }}
          </p>
        </div>
        <p v-if="frontmatter.date" class="type-code text-[var(--slate-40)]">
          {{ frontmatter.date }}
        </p>
      </div>
    </div>

    <!-- Visual side -->
    <div class="relative dakr:bg-[var(--slate-100)] overflow-hidden">
      <slot name="visual">
        <CosmicStrings
          :strings="8"
          :grid-x="20"
          :grid-y="20"
          :grid-z="10"
          :width="500"
          :height="500"
        />
      </slot>
    </div>
  </div>
</template>
