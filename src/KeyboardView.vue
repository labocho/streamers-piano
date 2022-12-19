<template>
<div>
  <svg width="420">
    <OctaveView :offsetX="0" :notesOn="notesOn" :c="48"></OctaveView>
    <OctaveView :offsetX="210" :notesOn="notesOn" :c="60"></OctaveView>
  </svg>
</div>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue'
import OctaveView from "./KeyboardView/OctaveView.vue"

export default defineComponent({
  name: "KeyboardView",
  components: { OctaveView },
  props: {
    notesOn: {
      type: Set as PropType<Set<number>>,
      required: true,
    },
  },
  setup() {
    return {}
  },
})
</script>

