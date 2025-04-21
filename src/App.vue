<script setup>
import Expander from './components/Expander.vue';
import { ref } from 'vue';
const rev = ref(false);
const doReveal = () => rev.value = true;
</script>

<template>
  <div>
    <Expander :reveal="rev">
      One
      <template #reveal>
        Seventy Five
      </template>
    </Expander>
    <button class="rev-button" @click="doReveal">
      Reveal
    </button>
  </div>
</template>

<style scoped>
.rev-button{
  position: fixed;
  top: 0;
  left: 0;
}
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
