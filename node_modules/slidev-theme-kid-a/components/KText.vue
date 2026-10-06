<script setup lang="ts">
type KTextVariant =
  | "heading-01"
  | "heading-02"
  | "heading-03"
  | "heading-04"
  | "heading-05"
  | "heading-06"
  | "heading-07"
  | "heading-08"
  | "heading-09"
  | "heading-10"
  | "body-compact-01"
  | "body-compact-02"
  | "body-short-01"
  | "body-short-02"
  | "body-long-01"
  | "body-long-02"
  | "code-01"
  | "code-02"
  | "label-01"
  | "label-02"
  | "helper-text-01"
  | "helper-text-02"
  | "caption-01"
  | "caption-02"
  | "legal-01"
  | "legal-02"
  | "overline-01"
  | "overline-02"
  | "quotation-01"
  | "quotation-02"
  | "paragraph-01"
  | "paragraph-02"
  | "display-01"
  | "display-02"
  | "display-03"
  | "display-04"




const props = withDefaults(defineProps<{
  variant?: KTextVariant
}>(), {
  variant: "body-02",
})
</script>

<template>
  <span
    class="ktext"
    :class="`ktext--${props.variant}`"
  >
    <slot />
  </span>
</template>
