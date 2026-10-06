<script setup lang="ts">
import { handleBackground } from "@slidev/client/layoutHelper";
import { computed } from "vue";

const props = defineProps({
  background: {
    type: String,
    default: undefined,
  },
});
</script>

<template>
  <div class="slidev-layout flex flex-row m-0 p-0 h-full w-full" :style="style">
    <div
      class="flex flex-row justify-end items-center w-full h-full light:bg-[var(--orion-60)] light:text-[var(--slate-10)] dark:text-[var(--orion-30)] dark:bg-[var(--slate-90)] p-4"
    >
      <slot name="header" />
    </div>
    <div
      class="flex flex-row justify-start items-center w-full h-full p-4
      light:bg-[var(--slate-10)] dark:bg-[var(--slate-100)]
      "
    >
      <slot name="section-summary" />
    </div>
  </div>
</template>
