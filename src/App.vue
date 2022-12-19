<template>
  <img alt="Vue logo" src="./assets/logo.png">
  <HelloWorld msg="Welcome to Your Vue.js + TypeScript App"/>
  <select v-if="midi" v-model="currentInput">
    <option :value="null">Please select input</option>
    <option v-for="input in inputs" :key="input.id" :value="input">
      {{ input.name }}
    </option>
  </select>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, Ref, computed, watch } from 'vue';
import HelloWorld from './components/HelloWorld.vue';

function parseMIDIMessage(data: Uint8Array) {
  switch(data[0]) {
    case 144: // Note on
      return {
        type: "NOTE_ON",
        pitch: data[1],
        velocity: data[2],
      };
    case 128: // Note on
      return {
        type: "NOTE_OFF",
        pitch: data[1],
        velocity: data[2],
      };
    default: // Other
      return null;
  }
}

export default defineComponent({
  name: 'App',
  components: {
    HelloWorld
  },
  setup() {
    // eslint-disable-next-line
    let midi: Ref<WebMidi.MIDIAccess|null> = ref(null);
    let inputs = computed(() => {
      return midi.value === null ? [] : Array.from(midi.value.inputs.values());
    });
    let currentInput: Ref<WebMidi.MIDIInput|null> = ref(null);

    onMounted(() => {
      window.navigator.requestMIDIAccess().then((m) => {
        midi.value = m;
      });
    });

    const inputEventHandler = (message: WebMidi.MIDIMessageEvent) => {
      console.log(parseMIDIMessage(message.data));
    };

    watch(currentInput, (newValue, oldValue) => {
      if (oldValue !== null) oldValue.removeEventListener("midimessage", inputEventHandler as EventListenerOrEventListenerObject);
      if (newValue !== null) newValue.addEventListener("midimessage", inputEventHandler);
    })

    return {
      currentInput,
      inputs,
      midi,
    };
  }
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
