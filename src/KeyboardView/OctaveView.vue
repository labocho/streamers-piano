<template>
<g :transform="`translate(${offsetX},0)`">
  <g>
    <rect :x="22.5 * 0" y="0" width="22.5" height="150" :fill="keyState[0] ? '#f00' : '#fff'" stroke="#000" />
    <rect :x="22.5 * 1" y="0" width="22.5" height="150" :fill="keyState[2] ? '#f00' : '#fff'" stroke="#000" />
    <rect :x="22.5 * 2" y="0" width="22.5" height="150" :fill="keyState[4] ? '#f00' : '#fff'" stroke="#000" />
    <rect :x="22.5 * 3" y="0" width="22.5" height="150" :fill="keyState[5] ? '#f00' : '#fff'" stroke="#000" />
    <rect :x="22.5 * 4" y="0" width="22.5" height="150" :fill="keyState[7] ? '#f00' : '#fff'" stroke="#000" />
    <rect :x="22.5 * 5" y="0" width="22.5" height="150" :fill="keyState[9] ? '#f00' : '#fff'" stroke="#000" />
    <rect :x="22.5 * 6" y="0" width="22.5" height="150" :fill="keyState[11] ? '#f00' : '#fff'" stroke="#000" />
  </g>
  <g>
    <rect :x="22.5 * 0 + (22.5 - 10)" y="0" width="15" height="97" :fill="keyState[1] ? '#f00' : '#000'" stroke="#000" />
    <rect :x="22.5 * 1 + (22.5 - 5)" y="0" width="15" height="97" :fill="keyState[3] ? '#f00' : '#000'" stroke="#000" />
    <rect :x="22.5 * 3 + (22.5 - 10)" y="0" width="15" height="97" :fill="keyState[6] ? '#f00' : '#000'" stroke="#000" />
    <rect :x="22.5 * 4 + (22.5 - 7)" y="0" width="15" height="97" :fill="keyState[8] ? '#f00' : '#000'" stroke="#000" />
    <rect :x="22.5 * 5 + (22.5 - 3.5)" y="0" width="15" height="97" :fill="keyState[10] ? '#f00' : '#000'" stroke="#000" />
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

