<template>
<g :transform="`translate(${offsetX},0)`">
  <g>
    <rect x="0" y="0" width="30" height="100" :fill="keyState[0] ? '#f00' : '#fff'" stroke="#000" />
    <rect x="30" y="0" width="30" height="100" :fill="keyState[2] ? '#f00' : '#fff'" stroke="#000" />
    <rect x="60" y="0" width="30" height="100" :fill="keyState[4] ? '#f00' : '#fff'" stroke="#000" />
    <rect x="90" y="0" width="30" height="100" :fill="keyState[5] ? '#f00' : '#fff'" stroke="#000" />
    <rect x="120" y="0" width="30" height="100" :fill="keyState[7] ? '#f00' : '#fff'" stroke="#000" />
    <rect x="150" y="0" width="30" height="100" :fill="keyState[9] ? '#f00' : '#fff'" stroke="#000" />
    <rect x="180" y="0" width="30" height="100" :fill="keyState[11] ? '#f00' : '#fff'" stroke="#000" />
  </g>
  <g>
    <rect x="20" y="0" width="20" height="60" :fill="keyState[1] ? '#f00' : '#000'" stroke="#000" />
    <rect x="50" y="0" width="20" height="60" :fill="keyState[3] ? '#f00' : '#000'" stroke="#000" />
    <rect x="110" y="0" width="20" height="60" :fill="keyState[6] ? '#f00' : '#000'" stroke="#000" />
    <rect x="140" y="0" width="20" height="60" :fill="keyState[8] ? '#f00' : '#000'" stroke="#000" />
    <rect x="170" y="0" width="20" height="60" :fill="keyState[10] ? '#f00' : '#000'" stroke="#000" />
  </g>
</g>
</template>

<script lang="ts">
import { defineComponent, PropType, computed, Ref } from 'vue'


export default defineComponent({
  name: "OctaveView",
  props: {
    c: {
      type: Number,
      required: true,
    },
    notesOn: {
      type: Set as PropType<Set<number>>,
      required: true,
    },
    offsetX: {
      type: Number,
      required: true
    }
  },
  setup(props) {
    const keyState: Ref<boolean[]> = computed(() => {
      return [...Array(12).keys()].map((i) => {
        return props.notesOn.has(props.c + i);
      });
    });
    return {
      keyState,
    }
  },
})
</script>

