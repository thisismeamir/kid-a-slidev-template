<!-- components/Mindmap.vue -->
<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { Transformer } from 'markmap-lib'
import { Markmap } from 'markmap-view'

const props = defineProps<{
  markdown: string   // outline as markdown, e.g. "# Root\n## Branch\n### Leaf"
}>()

const svgEl = ref<SVGElement>()
const transformer = new Transformer()

onMounted(() => {
  const { root } = transformer.transform(props.markdown)
  if (svgEl.value) {
    Markmap.create(svgEl.value, {
      color: () => 'var(--slate-30)',
      paddingX: 16,
      autoFit: true,
      duration: 0,
    }, root)
  }
})
</script>

<template>
  <div class="w-full h-full  mindmap-wrap">
    <svg ref="svgEl" class="w-full h-full" />
  </div>
</template>

<style>
.mindmap-wrap .markmap-node > circle {
  fill: var(--orion-50);
  stroke: none;
}
.mindmap-wrap .markmap-node > text {
  font-family: var(--font-sans);
  fill: var(--slate-100);
}
.mindmap-wrap .markmap-link {
  stroke: var(--slate-30);
  stroke-width: 0.5;
  border-radius: 0;
}
</style>
