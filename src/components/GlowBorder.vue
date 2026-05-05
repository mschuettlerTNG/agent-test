<script setup>
import { computed } from 'vue'

const props = defineProps({
  borderRadius: { type: Number, default: 10 },
  color: { type: [String, Array], default: '#FFF' },
  borderWidth: { type: Number, default: 2 },
  duration: { type: Number, default: 10 },
})

const styles = computed(() => ({
  '--border-radius': `${props.borderRadius}px`,
  '--border-width': `${props.borderWidth}px`,
  '--duration': `${props.duration}s`,
  backgroundImage: `radial-gradient(transparent,transparent, ${
    Array.isArray(props.color) ? props.color.join(',') : props.color
  },transparent,transparent)`,
  backgroundSize: '300% 300%',
  mask: 'linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0)',
  WebkitMask: 'linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0)',
  WebkitMaskComposite: 'xor',
  maskComposite: 'exclude',
  padding: 'var(--border-width)',
  borderRadius: 'var(--border-radius)',
}))
</script>

<template>
  <div :style="styles" class="glow-border" />
</template>

<style scoped>
@keyframes glow {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

.glow-border {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  border-radius: inherit;
  animation: glow var(--duration) ease infinite;
  will-change: background-position;
}
</style>
