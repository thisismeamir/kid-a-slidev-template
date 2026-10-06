<!-- layouts/full.vue -->
<script setup lang="ts">
import type { Frontmatter } from '@slidev/types'

interface FullFrontmatter extends Frontmatter {
  image?: string   // background-style single image, if not using slot
}

const { frontmatter } = defineProps<{
  frontmatter: FullFrontmatter
}>()
</script>

<template>
  <div class="slidev-layout full h-full w-full m-0 p-0 bg-[var(--slate-10)] flex items-center justify-center overflow-hidden">
    <slot>
      <img
        v-if="frontmatter.image"
        :src="frontmatter.image"
        class="max-w-full max-h-full w-auto h-auto object-contain"
      />
    </slot>
  </div>
</template>
