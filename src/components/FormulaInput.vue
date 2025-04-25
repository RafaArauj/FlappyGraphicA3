<template>
  <input
    :value="internalValue"
    @input="onInput"
    placeholder="Digite a fórmula (ex: Math.sin(x))"
    class="border p-2 w-full"
  />
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps(['modelValue'])
const emit = defineEmits(['update:modelValue'])

const internalValue = ref(props.modelValue)

// Atualiza o valor interno se o prop mudar externamente
watch(() => props.modelValue, (newVal) => {
  internalValue.value = newVal
})

function onInput(event) {
  internalValue.value = event.target.value
  emit('update:modelValue', internalValue.value)
}
</script>
