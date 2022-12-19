<template>
  <KeyboardView :notesOn="notesOn"></KeyboardView>
  <code>{{ notesOnJoined }}</code>
  <select v-if="midi" v-model="currentInput">
    <option :value="null">Please select input</option>
    <option v-for="input in inputs" :key="input.id" :value="input">
      {{ input.name }}
    </option>
  </select>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, Ref, computed, watch } from 'vue';
import KeyboardView from "./KeyboardView.vue"

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
  components: { KeyboardView },
  setup() {
    // eslint-disable-next-line
    let midi: Ref<WebMidi.MIDIAccess|null> = ref(null);
    let inputs = computed(() => {
      return midi.value === null ? [] : Array.from(midi.value.inputs.values());
    });
    let currentInput: Ref<WebMidi.MIDIInput|null> = ref(null);
    let notesOn: Ref<Set<number>> = ref(new Set);
    let notesOnJoined: Ref<string> = computed(() => {
      return Array.from(notesOn.value).join(", ");
    });

    onMounted(() => {
      window.navigator.requestMIDIAccess().then((m) => {
        midi.value = m;
      });
    });

    const inputEventHandler = (message: WebMidi.MIDIMessageEvent) => {
      const parsed = parseMIDIMessage(message.data);
      if (parsed === null) return;

      const v = notesOn.value;
      switch(parsed.type) {
        case "NOTE_ON":
          if (v.has(parsed.pitch)) return;
          v.add(parsed.pitch)
          notesOn.value = v;
          break;
        case "NOTE_OFF":
          if (!v.delete(parsed.pitch)) return;
          notesOn.value = v;
          break;
      }
    };

    watch(currentInput, (newValue, oldValue) => {
      if (oldValue !== null) oldValue.removeEventListener("midimessage", inputEventHandler as EventListenerOrEventListenerObject);
      if (newValue !== null) newValue.addEventListener("midimessage", inputEventHandler);
    })

    return {
      currentInput,
      inputs,
      midi,
      notesOn,
      notesOnJoined,
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
