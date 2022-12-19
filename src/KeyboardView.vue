<template>
<div class="keyboard-container">
  <svg :viewBox="`0 0 ${157.5 * octaves} 150`">
    <OctaveView
      v-for="i in octavesAsArray"
      :offsetX="157.5 * i"
      :c="lowestC + 12 * i"
      :notesOn="notesOn"
      :key="i"
    >
    </OctaveView>
  </svg>
</div>
</template>

<script lang="ts">
import { defineComponent, PropType, computed } from 'vue'
import OctaveView from "./KeyboardView/OctaveView.vue"

export default defineComponent({
  name: "KeyboardView",
  components: { OctaveView },
  props: {
    notesOn: {
      type: Set as PropType<Set<number>>,
      required: true,
    },
    octaves: {
      type: Number,
      required: true,
    },
    lowestC: {
      type: Number,
      required: true,
    }
  },
  setup(props) {
    const octavesAsArray = computed(() => {
      return [...new Array(props.octaves)].map((_, i ) => i);
    });
    return {
      octavesAsArray,
    }
  },
})
</script>

<style scoped>
.keyboard-container {
  width: 100%;
}
</style>
