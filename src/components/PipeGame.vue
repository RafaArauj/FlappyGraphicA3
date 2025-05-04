<script setup>
    import { ref } from 'vue'
    import FormulaInput from './FormulaInput.vue'
    import GraphCanvas from './GraphCanvas.vue'
    
    let tube1 = Math.floor(Math.random() * 7)-3;
    let tube2 = Math.floor(Math.random() * 7)-3;
    let tube3 = Math.floor(Math.random() * 7)-3; 

    const formula = ref('Math.sin(x)')
    const pipes = ref([
      { x: 3, yMin: tube1, yMax: tube1+3 },
      { x: 6, yMin: tube2, yMax: tube2+3 },
      { x: 9, yMin: tube3, yMax: tube3+3 }
    ])
</script>

<template>
  <div class="flex flex-col justify-center items-center">
    <FormulaInput v-model="formula" />
    <GraphCanvas :formula="formula" :pipes="pipes" />
  </div>
</template>
