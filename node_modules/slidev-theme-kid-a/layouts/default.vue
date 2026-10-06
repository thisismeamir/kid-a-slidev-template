<script setup lang="ts">
import { handleBackground } from '@slidev/client/layoutHelper'
import { computed } from 'vue'

const props = defineProps({
  background: {
    type: String,
    default: undefined,
  },
})

</script>

<template>
  <div class="slidev-layout intro" :style="style">
    <div class="my-auto">
      <slot />
    </div>
  </div>
</template>
