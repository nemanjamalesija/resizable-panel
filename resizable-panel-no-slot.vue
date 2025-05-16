<script setup lang="ts">
import { ref, computed, watch } from 'vue'
import { useEventListener } from '@vueuse/core'

const props = defineProps({
  translate: {
    type: Number,
    required: true,
  },
})
const emit = defineEmits(['update:translate'])

const translateY = ref(props.translate)
const isResizing = ref(false)

watch(() => props.translate, (val) => {
  translateY.value = val
})

const panelStyle = computed(() => {
  return {
    transform: `translateY(-${translateY.value - 8}px)`,
  }
})

const onMouseDown = (e: MouseEvent) => {
  e.preventDefault()
  isResizing.value = true
}

const onMouseMove = (e: MouseEvent) => {
  if (!isResizing.value) return

  const maxTranslate = window.innerHeight * 0.8
  let newTranslate = window.innerHeight - e.clientY

   if (newTranslate > maxTranslate) {
    newTranslate = maxTranslate
  }

  translateY.value = newTranslate
  emit('update:translate', newTranslate)
}

const onMouseUp = () => {
  if (isResizing.value) {
    isResizing.value = false
  }
}

useEventListener(window, 'mousemove', onMouseMove)
useEventListener(window, 'mouseup', onMouseUp)
</script>

<template>
  <div
    class="h-3 w-full fixed left-0 absolute bottom-0 z-9999 cursor-col-resize hover:bg-(--ui-border) transition-colors duration-300 ease-in-out"
    :class="[isResizing && 'bg-(--ui-border) cursor-col-resize']"
    :style="panelStyle"
    @mousedown="onMouseDown"
  />
</template>
