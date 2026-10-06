<!-- components/Kicker.vue -->
<script setup lang="ts">
defineProps<{
  index?: string   // e.g. "01"
  label: string    // e.g. "INTRODUCTION"
}>()
</script>

<template>
  <div class="flex items-center gap-[var(--space-2)]">
    <span
      v-if="index"
      class="type-code text-[var(--orion-50)] font-[var(--weight-medium)]"
    >
      {{ index }}
    </span>
    <span
      class="type-label uppercase tracking-[var(--tracking-wide)] text-[var(--slate-60)]"
    >
      {{ label }}
    </span>
    <div class="flex-1 h-[1px] bg-[var(--slate-20)]" />
  </div>
</template>
