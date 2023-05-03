<template>
  <span
    class="nuxt-icon"
    :class="{
      'nuxt-icon--fill': !filled,
      'nuxt-icon--stroke': hasStroke && !filled,
    }"
    v-html="icon"
  />
</template>

<script setup lang="ts">
import { ref, watchEffect } from '#imports'

const props = withDefaults(
  defineProps<{
    name: string
    svgClass?: string
    filled?: boolean
  }>(),
  { filled: false, svgClass: "" }
)

const icon = ref<string | Record<string, any>>('')
let hasStroke = false

async function getIcon() {
  try {
    const iconsImport = import.meta.glob('assets/icons/**/**.svg', {
      as: 'raw',
      eager: false,
    })
    let rawIcon = await iconsImport[`/assets/icons/${props.name}.svg`]()
    if (rawIcon.includes('stroke')) {
      hasStroke = true
    }
    if (props.svgClass) {
      rawIcon = rawIcon.replace("<svg ", `<svg class="${props.svgClass}" `)
    }
    icon.value = rawIcon
  } catch {
    console.error(
      `[nuxt-icons] Icon '${props.name}' doesn't exist in 'assets/icons'`
    )
  }
}

await getIcon()

watchEffect(getIcon)
</script>

<style scoped>
.nuxt-icon svg {
  vertical-align: middle;
}
.nuxt-icon.nuxt-icon--fill,
.nuxt-icon.nuxt-icon--fill * {
  fill: currentColor !important;
}

.nuxt-icon.nuxt-icon--stroke,
.nuxt-icon.nuxt-icon--stroke * {
  stroke: currentColor !important;
}
</style>
