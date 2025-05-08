<!-- UnifiedComponent.vue -->
<template>
  <div>
    <!-- Seção do jogo de canos com input da fórmula e gráfico -->
    <div class="flex flex-col justify-center items-center">
      
      <!-- Canvas onde o gráfico é desenhado -->
      <canvas ref="canvas" width="600" height="400"></canvas>
      
      <!-- Campo de input da fórmula matemática -->
      <input
        :value="formula"
        @input="onInput"
        placeholder="Digite a fórmula (ex: Math.sin(x))"
        class="border p-2 w-full"
      />  
    </div>
  </div>
</template>

<script setup>
/**
 * Importando funcionalidades do Vue
 * - ref: para reatividade
 * - watch: para reagir a mudanças de variáveis
 * - onMounted: para executar código após o componente ser montado
 */
import { ref, watch, onMounted } from 'vue'

// ---------------------
// Fórmula e Input
// ---------------------

// Valor reativo da fórmula inserida pelo usuário
const formula = ref('Math.sin(x)')

// Função que atualiza a fórmula quando o usuário digita no input
function onInput(event) {
  formula.value = event.target.value
}

// ---------------------
// Geração aleatória dos canos
// ---------------------

// Gera valores aleatórios para os canos (entre -3 e 3)
let tube1 = Math.floor(Math.random() * 7) - 3.5
let tube2 = Math.floor(Math.random() * 8) - 3.5
let tube3 = Math.floor(Math.random() * 8) - 3.5

// Lista reativa de canos com posições e tamanhos baseados nos valores acima
const pipes = ref([
  { x: 3, yMin: tube1 +0.5, yMax: tube1 + 3.5 },
  { x: 6, yMin: tube2 +0.5, yMax: tube2 + 3.5 },
  { x: 9, yMin: tube3 +0.5, yMax: tube3 + 3.5 }
])

// ---------------------
// Canvas e desenho do gráfico
// ---------------------

// Referência ao elemento canvas no DOM
const canvas = ref(null)

// Função que desenha o gráfico e os canos no canvas
function drawGraph() {
  const ctx = canvas.value.getContext('2d') // contexto 2D
  ctx.clearRect(0, 0, 600, 400) // limpa o canvas

  // Desenha os canos em verde
  ctx.fillStyle = 'green'
  for (const pipe of pipes.value) {
    // Parte superior do cano
    ctx.fillRect(pipe.x * 50, 0, 10, 200 - pipe.yMin * 50)
    // Parte inferior do cano
    ctx.fillRect(pipe.x * 50, 400 - pipe.yMax * 50, 10, 400)
  }
  
  // Variavel que vai segurar o valor de se o graphico plotado não colide com os tubos
  let correctGraph = false
  
  try {
    // Cria uma função baseada na fórmula digitada
    const formulaFunc = new Function('x', `return ${formula.value}`)
    
    
    // Inicia o caminho da curva azul
    ctx.beginPath()
    if( 
      formulaFunc(3) <= tube1 + 0.5 &&
      formulaFunc(3.2) <= tube1 + 0.5 && 
      formulaFunc(3) >= tube1 - 0.5 &&
      formulaFunc(3.2) >= tube1 - 0.5 &&
      formulaFunc(6) <= tube2 + 0.5 &&
      formulaFunc(6.2) <= tube2 + 0.5 && 
      formulaFunc(6) >= tube2 - 0.5 &&
      formulaFunc(6.2) >= tube2 - 0.5 &&
      formulaFunc(9) <= tube3 + 0.5 &&
      formulaFunc(9.2) <= tube3 + 0.5 && 
      formulaFunc(9) >= tube3 - 0.5 &&
      formulaFunc(9.2) >= tube3 - 0.5 
      ){
      console.log("went thru")
      ctx.strokeStyle = 'blue'
      correctGraph = true
    }else{
      ctx.strokeStyle = 'red'
      correctGraph = false
    }
    
    // Desenha a curva no intervalo de x = 0 até x = 12
    for (let x = 0; x <= 12; x += 0.05) {
      const y = formulaFunc(x)
      const canvasX = x * 50
      const canvasY = 200 - y * 50 // ajusta Y para o centro vertical

      // Move o cursor ou desenha linha dependendo da posição
      if (x === 0) ctx.moveTo(canvasX, canvasY)
      else ctx.lineTo(canvasX, canvasY)
    }

    ctx.stroke() // finaliza o desenho da curva
  } catch (e) {
    console.warn('Erro na fórmula', e)
  }
}

// ---------------------
// Hooks reativos
// ---------------------

// Chama drawGraph quando o componente for montado
onMounted(drawGraph)

// Redesenha quando a fórmula mudar
watch(formula, drawGraph)

// Redesenha se os canos mudarem (embora eles sejam fixos nesse exemplo)
watch(pipes, drawGraph)
</script>

<style scoped>
/* Estilo básico do canvas */
canvas {
  border: 1px solid #ccc;
  background-color: #f9f9f9;
}
</style>
