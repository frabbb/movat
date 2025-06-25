<script setup>
import { ref, watch, onMounted } from 'vue'

const props = defineProps({
  label: String,
  min: Number,
  max: Number,
  modelValue: Number,
})

const emit = defineEmits(['update:modelValue'])

const input = ref(null)

const updateValue = (e) => {
  const value = e.target.value
  emit('update:modelValue', Number(value))
  updateThumbPosition(value)
}

const updateThumbPosition = (value) => {
  if (input.value) {
    const percentage = ((value - props.min) / (props.max - props.min)) * 100
    input.value.style.setProperty('--value', percentage)
  }
}

onMounted(() => {
  updateThumbPosition(props.modelValue)
})

watch(
  () => props.modelValue,
  (newValue) => {
    updateThumbPosition(newValue)
  },
  { immediate: true },
)
</script>

<template>
  <div>
    <p class="title">{{ label }}</p>
    <div class="input-container">
      <p class="label">{{ min }}</p>
      <input
        type="range"
        :min="min"
        :max="max"
        :value="modelValue"
        @input="updateValue"
        ref="input"
      />
      <p class="label">{{ max }}</p>
      <input type="number" :min="min" :max="max" :value="modelValue" @input="updateValue" />
    </div>
  </div>
</template>

<style scoped>
.input-container {
  display: flex;
  align-items: center;
  gap: var(--m);
}

input[type='range'] {
  accent-color: black;
  -webkit-appearance: none;
  appearance: none;
  cursor: pointer;
  flex: 1;
  background: rgba(255, 255, 255, 0.3);
  overflow: hidden;
}

input[type='range']::-webkit-slider-thumb {
  -webkit-appearance: none;
  background: white;
  width: 1px;
  height: 25px;
  box-shadow: -400px 0 0 400px rgba(255, 255, 255, 0.3);
}

.title {
  padding-bottom: 4px;
}

.label {
  color: rgba(255, 255, 255, 0.7);
}

input[type='number'] {
  padding: var(--s);
  border-radius: var(--s);
  border: 1px solid black;
  text-align: center;
  background: transparent;
  margin-left: var(--l);
  width: 60px;
  color: white;
  border-color: rgba(255, 255, 255, 0.3);
}
</style>
