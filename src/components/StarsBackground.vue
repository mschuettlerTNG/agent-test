<script setup>
/**
 * Stars Background - adapted from inspira-ui
 * @see https://inspira-ui.com/en/components/backgrounds/stars-background
 *
 * A parallax-animated starfield background using layered motion
 * and randomized star positions.
 */
import { motion, useMotionValue, useSpring } from 'motion-v'
import { computed, onMounted, ref, watch } from 'vue'

const props = defineProps({
  factor: { type: Number, default: 0.05 },
  speed: { type: Number, default: 50 },
  transition: {
    type: Object,
    default: () => ({ stiffness: 50, damping: 20 }),
  },
  starColor: { type: String, default: '#fff' },
})

defineSlots()

function generateStars(count, color) {
  const shadows = []
  for (let i = 0; i < count; i++) {
    const x = Math.floor(Math.random() * 4000) - 2000
    const y = Math.floor(Math.random() * 4000) - 2000
    shadows.push(`${x}px ${y}px ${color}`)
  }
  return shadows.join(', ')
}

const offsetX = useMotionValue(1)
const offsetY = useMotionValue(1)

const springX = useSpring(offsetX, props.transition)
const springY = useSpring(offsetY, props.transition)

function handleMouseMove(e) {
  const centerX = window.innerWidth / 2
  const centerY = window.innerHeight / 2
  offsetX.set(-(e.clientX - centerX) * props.factor)
  offsetY.set(-(e.clientY - centerY) * props.factor)
}

const boxShadow1 = ref('')
const boxShadow2 = ref('')
const boxShadow3 = ref('')

onMounted(() => {
  boxShadow1.value = generateStars(1000, props.starColor)
  boxShadow2.value = generateStars(400, props.starColor)
  boxShadow3.value = generateStars(200, props.starColor)
})

watch(
  () => props.starColor,
  (color) => {
    boxShadow1.value = generateStars(1000, color)
    boxShadow2.value = generateStars(400, color)
    boxShadow3.value = generateStars(200, color)
  },
)

const layer1Transition = computed(() => ({
  repeat: Infinity,
  duration: props.speed,
  ease: 'linear',
}))

const layer2Transition = computed(() => ({
  repeat: Infinity,
  duration: props.speed * 2,
  ease: 'linear',
}))

const layer3Transition = computed(() => ({
  repeat: Infinity,
  duration: props.speed * 3,
  ease: 'linear',
}))
</script>

<template>
  <div class="stars-container" @mousemove="handleMouseMove">
    <motion.div :style="{ x: springX, y: springY }">
      <!-- Layer 1: small stars (1px) -->
      <motion.div
        class="star-layer"
        :animate="{ y: [0, -2000] }"
        :transition="layer1Transition"
      >
        <div class="star-dot" :style="{ width: '1px', height: '1px', boxShadow: boxShadow1 }" />
        <div class="star-dot star-dot-clone" :style="{ width: '1px', height: '1px', boxShadow: boxShadow1 }" />
      </motion.div>

      <!-- Layer 2: medium stars (2px) -->
      <motion.div
        class="star-layer"
        :animate="{ y: [0, -2000] }"
        :transition="layer2Transition"
      >
        <div class="star-dot" :style="{ width: '2px', height: '2px', boxShadow: boxShadow2 }" />
        <div class="star-dot star-dot-clone" :style="{ width: '2px', height: '2px', boxShadow: boxShadow2 }" />
      </motion.div>

      <!-- Layer 3: large stars (3px) -->
      <motion.div
        class="star-layer"
        :animate="{ y: [0, -2000] }"
        :transition="layer3Transition"
      >
        <div class="star-dot" :style="{ width: '3px', height: '3px', boxShadow: boxShadow3 }" />
        <div class="star-dot star-dot-clone" :style="{ width: '3px', height: '3px', boxShadow: boxShadow3 }" />
      </motion.div>
    </motion.div>

    <!-- Child content renders above the stars -->
    <slot />
  </div>
</template>

<style scoped>
.stars-container {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
  background: radial-gradient(ellipse at bottom, #262626 0%, #000 100%);
}

.star-layer {
  position: absolute;
  top: 0;
  left: 0;
  height: 2000px;
  width: 100%;
}

.star-dot {
  position: absolute;
  border-radius: 9999px;
  background: transparent;
}

.star-dot-clone {
  top: 2000px;
}
</style>
