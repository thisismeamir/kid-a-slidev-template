<!-- components/SlideFooter.vue -->
<script setup lang="ts">
import { useNav } from '@slidev/client'

defineProps<{
  title?: string     // paper / talk title
  author?: string     // presenter name
}>()

const { currentPage, total } = useNav()
</script>

<template>
  <div
    class="absolute bottom-0 left-0 right-0 flex items-center justify-between
           px-[var(--space-4)] py-[var(--space-2)]
           border-t border-[var(--slate-20)]"
  >
    <div class="flex items-center gap-[var(--space-3)]">
      <span v-if="author" class="type-label text-[var(--slate-50)]">
        {{ author }}
      </span>
      <span v-if="title" class="type-label text-[var(--slate-40)]">
        {{ title }}
      </span>
    </div>

    <div class="flex items-center gap-[var(--space-1)]">
      <span class="type-code text-[var(--orion-50)]">
        {{ String(currentPage).padStart(2, '0') }}
      </span>
      <span class="type-code text-[var(--slate-40)]">
        / {{ String(total).padStart(2, '0') }}
      </span>
    </div>
  </div>
</template>
