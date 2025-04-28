<script setup>
import { onMounted, watch, ref } from 'vue'

const props = defineProps(['formula', 'pipes'])
const canvas = ref(null)

function drawGraph() {
  const ctx = canvas.value.getContext('2d')
  ctx.clearRect(0, 0, 600, 400)

  // Desenhar os "canos"
  ctx.fillStyle = 'green'
  for (const pipe of props.pipes) {
    ctx.fillRect(pipe.x * 50, 0, 10, 200 - pipe.yMin * 50)
    ctx.fillRect(pipe.x * 50, 400 - pipe.yMax * 50, 10, 400)
  }

  // Desenhar a curva
  ctx.beginPath()
  ctx.strokeStyle = 'blue'

  try {
    const formulaFunc = new Function('x', `return ${props.formula}`)
    for (let x = 0; x <= 12; x += 0.05) {
      const y = formulaFunc(x)
      const canvasX = x * 50
      const canvasY = 200 - y * 50
      if (x === 0) ctx.moveTo(canvasX, canvasY)
      else ctx.lineTo(canvasX, canvasY)
    }
    ctx.stroke()
  } catch (e) {
    console.warn('Erro na fórmula', e)
  }
}

onMounted(drawGraph)
watch(() => props.formula, drawGraph)
watch(() => props.pipes, drawGraph)
</script>

<template>
  <canvas ref="canvas" width="600" height="400"></canvas>
</template>

<style scoped>
canvas {
  border: 1px solid #ccc;
  background-color: #f9f9f9;
}
</style>
