<template>
<div class="container" style="height: 100%; display: flex; flex-direction: column;">
  <form style="flex-grow: 0; display: flex; padding: 1em 0 2em;">
      <div class="me-3">
        <label class="form-label" for="">MIDI Device</label>
        <select class="form-select" v-if="midi" v-model="currentInput">
          <option :value="null">Please select input</option>
          <option v-for="input in inputs" :key="input.id" :value="input">
            {{ input.name }}
          </option>
        </select>
      </div>
      <div class="me-3">
        <label class="form-label" for="">Lowest C</label>
        <select class="form-select" v-model.number="lowestC">
          <option :value="12">C0</option>
          <option :value="24">C1</option>
          <option :value="36">C2</option>
          <option :value="48">C3</option>
          <option :value="60">C4</option>
          <option :value="72">C5</option>
          <option :value="84">C6</option>
        </select>
      </div>
        <div class="me-3">
        <label class="form-label" for="">Octaves</label>
        <select class="form-select" v-model.number="octaves">
          <option :value="1">1</option>
          <option :value="2">2</option>
          <option :value="3">3</option>
          <option :value="4">4</option>
          <option :value="5">5</option>
          <option :value="6">6</option>
          <option :value="7">7</option>
        </select>
      </div>
  </form>
  <div style="flex-grow: 1; display: flex; align-items: center;">
    <KeyboardView v-if="currentInput" :notesOn="notesOn" :octaves="octaves" :lowestC="lowestC"></KeyboardView>
  </div>
</div>
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
    const midi: Ref<WebMidi.MIDIAccess|null> = ref(null);
    const inputs = computed(() => {
      return midi.value === null ? [] : Array.from(midi.value.inputs.values());
    });
    const currentInput: Ref<WebMidi.MIDIInput|null> = ref(null);
    const notesOn: Ref<Set<number>> = ref(new Set);
    const lowestC: Ref<number> = ref(48);
    const octaves: Ref<number> = ref(2);

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
      lowestC,
      midi,
      notesOn,
      octaves,
    };
  }
});
</script>

<style>
</style>
