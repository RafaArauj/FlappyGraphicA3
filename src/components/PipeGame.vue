<!-- UnifiedComponent.vue -->
<template>
  <div>
    <!-- Seção do jogo de canos com input da fórmula e gráfico -->
    <div class="flex flex-col justify-center items-center">
      <div class="timesTried" id="timesTried">0</div>

      <!-- Canvas onde o gráfico é desenhado -->
      <canvas ref="canvas" width="600" height="400"></canvas>

      <!-- Campo de input da fórmula matemática -->
      <div class="mathInput">
        <input
          :value="formula"
          @keyup.enter="doTry"
          @input="onInput"
          placeholder="Digite a fórmula (ex: sin(x)*2)"
          class="inputContainer border p-2"
        />
        <button class="inputButton" @click="doTry">></button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
import { create, all } from "mathjs";

const math = create(all, {});
// Referência ao elemento canvas no DOM
const canvas = ref(null);
// Variavel que vai segurar o valor de se o graphico plotado não colide com os tubos
let correctGraph = false;
// Variavel que vai segurar o valor de quantas tentativas o usuario ja fez
let triesCount = 0;
// Valor reativo da fórmula inserida pelo usuário
const formula = ref("sin(x)");

// Função que atualiza a fórmula quando o usuário digita no input
function onInput(event) {
  formula.value = event.target.value;
}

// Função que atualiza o grafico quando o usuário envia uma formula
function doTry(event) {
  drawGraph();
  triesCount = triesCount + 1;
  let score = document.getElementById("timesTried");
  score.innerText = triesCount;
}

// Gera valores aleatórios para os canos (entre -3 e 3)
let tubes = [];
for (let x = 1; x <= 3; x++) {
  tubes[x] = Math.floor(Math.random() * 8) - 3.5;
}

// Lista reativa de canos com posições e tamanhos baseados nos valores acima
const pipes = ref([
  { x: 3, yMin: tubes[1] + 0.5, yMax: tubes[1] + 3.5 },
  { x: 6, yMin: tubes[2] + 0.5, yMax: tubes[2] + 3.5 },
  { x: 9, yMin: tubes[3] + 0.5, yMax: tubes[3] + 3.5 },
]);

// ---------------------
// Canvas e desenho do gráfico
// ---------------------

// Função que desenha o gráfico e os canos no canvas
function drawGraph() {
  const ctx = canvas.value.getContext("2d"); // contexto 2D
  ctx.clearRect(0, 0, 600, 400); // limpa o canvas

  try {
    // Cria uma função baseada na fórmula digitada
    function formulaFunc(x) {
      const expr = math.compile(formula.value);
      return expr.evaluate({ x });
    }

    // Inicia o caminho da curva azul
    ctx.beginPath();

    let hit = 12;

    function testColison(val) {
      return (
        formulaFunc(val + 0) > tubes[val / 3] + 0.5 ||
        formulaFunc(val + 0) < tubes[val / 3] - 0.5 ||
        formulaFunc(val + 0.2) > tubes[val / 3] + 0.5 ||
        formulaFunc(val + 0.2) < tubes[val / 3] - 0.5
      );
    }

    for (let x = 9; x > 0; x -= 3) {
      if (testColison(x)) {
        hit = x + 0.2;
      }
    }

    correctGraph = hit >= 12;

    if (correctGraph) {
      ctx.strokeStyle = "blue";
    } else {
      ctx.strokeStyle = "red";
    }

    // Desenha a curva no intervalo de x = 0 até x = 12
    for (let x = 0; x <= hit; x += 0.05) {
      const y = formulaFunc(x);
      const canvasX = x * 50;
      const canvasY = 200 - y * 50; // ajusta Y para o centro vertical

      // Move o cursor ou desenha linha dependendo da posição
      if (x === 0) ctx.moveTo(canvasX, canvasY);
      else ctx.lineTo(canvasX, canvasY);
    }

    ctx.stroke(); // finaliza o desenho da curva
  } catch (e) {
    let eString = (e + "")
    if (eString.includes("Error: Undefined function")) {
      alert(`Operação desconhecida: ${eString.replace("Error: Undefined function","")}`);
    }
  }

  // Desenha os canos em verde
  ctx.fillStyle = "green";
  for (const pipe of pipes.value) {
    // Parte superior do cano
    ctx.fillRect(pipe.x * 50, 0, 10, 200 - pipe.yMin * 50);
    // Parte inferior do cano
    ctx.fillRect(pipe.x * 50, 400 - pipe.yMax * 50, 10, 400);
  }
}

// ---------------------
// Hooks reativos
// ---------------------

// Chama drawGraph quando o componente for montado
onMounted(drawGraph);

// Redesenha quando a fórmula mudar
//watch(formula, drawGraph);

// Redesenha se os canos mudarem (embora eles sejam fixos nesse exemplo)
watch(pipes, drawGraph);
</script>

<style scoped>
/* Estilo básico do canvas */
canvas {
  border-radius: 10px;
  background-color: #d9d9d9;
}
.mathInput {
  border-radius: 10px;
  width: 600px;
  margin-top: 10px;
  background-color: #d9d9d9;
}
.inputContainer {
  border-radius: 10px;
  padding: 15px;
  width: 100%;
  background-color: transparent;
}
.inputButton {
  border-radius: 10px;
  position: relative;
  margin-left: -50px;
  width: 45px;
  height: 45px;
  background-color: #adadad;
  transition: font-weight ease-in-out 0.15s;
}
.inputButton:hover {
  font-weight: bolder;
}
.inputButton:active {
  font-weight: lighter;
}
.timesTried {
  border-radius: 10px;
  translate: 0 30px 0;
  z-index: 5;
  margin-left: 535px;
  width: 50px;
  text-align: center;
  background-color: #adadad;
}
</style>
