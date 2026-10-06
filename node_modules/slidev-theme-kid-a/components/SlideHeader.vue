<!-- components/SlideHeader.vue -->
<script setup lang="ts">
defineProps<{
  index?: string     // section number, e.g. "02"
  section?: string   // section name, e.g. "METHODOLOGY"
  affiliation?: string // e.g. "CERN — EP-FCC"
}>()
</script>

<template>
  <div
    class="absolute top-0 left-0 right-0 flex items-center justify-between
           px-[var(--space-4)] py-[var(--space-3)]
           border-b border-[var(--slate-20)]"
  >
    <Kicker v-if="section" :index="index" :label="section" />
    <span v-else class="flex-1" />

    <span
      v-if="affiliation"
      class="type-code text-[var(--slate-40)] uppercase tracking-[var(--tracking-wide)]"
    >
      {{ affiliation }}
    </span>
  </div>
</template>
