<!-- components/MindmapNode.vue -->
<script setup lang="ts">
import { inject, onMounted, ref } from 'vue'

interface TreeNode {
  id: string
  label: string
  children?: TreeNode[]
}

const props = defineProps<{
  node: TreeNode
  depth?: number
}>()

const registerNode = inject<(id: string, el: HTMLElement) => void>('registerNode')!
const el = ref<HTMLElement>()

onMounted(() => {
  if (el.value) registerNode(props.node.id, el.value)
})

const styleFor = (depth = 0) => {
  if (depth === 0) return 'bg-[var(--orion-60)] text-[var(--slate-10)] type-label'
  if (depth === 1) return 'bg-[var(--slate-100)] text-[var(--slate-10)] type-label'
  return 'bg-[var(--slate-10)] text-[var(--slate-90)] type-code border border-[var(--slate-30)]'
}
</script>

<template>
  <div class="flex items-center gap-[var(--space-4)]">
    <div ref="el" :class="[styleFor(depth), 'px-[var(--space-3)] py-[var(--space-1)] whitespace-nowrap']">
      {{ node.label }}
    </div>

    <div v-if="node.children?.length" class="flex flex-col gap-[var(--space-2)]">
      <MindmapNode
        v-for="child in node.children"
        :key="child.id"
        :node="child"
        :depth="(depth ?? 0) + 1"
      />
    </div>
  </div>
</template>
