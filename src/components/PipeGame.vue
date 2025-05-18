<!-- UnifiedComponent.vue -->
<template>
  <div>
    <!-- Seção do jogo de canos com input da fórmula e gráfico -->
    <div class="flex flex-col justify-center items-center">
      <div class="timesTried" id="timesTried">0</div>

      <!-- Canvas onde o gráfico é desenhado -->
      <canvas ref="canvas" width="6000" height="4000"></canvas>

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
  let hold = Math.round(Math.random() * 4 - 3.6);
  tubes[x] = hold * 10;
}

// Lista reativa de canos com posições e tamanhos baseados nos valores acima
const pipes = ref([
  { x: 30, yMin: tubes[1] - 1, yMax: tubes[1] - 7 },
  { x: 60, yMin: tubes[2] - 1, yMax: tubes[2] - 7 },
  { x: 90, yMin: tubes[3] - 1, yMax: tubes[3] - 7 },
]);

// ---------------------
// Canvas e desenho do gráfico
// ---------------------

// Função que desenha o gráfico e os canos no canvas

let canvaswidth = 6000;
let canvasheight = 4000;

canvas.width = canvaswidth;
canvas.height = canvasheight;

function drawGraph() {
  const ctx = canvas.value.getContext("2d"); // contexto 2D
  ctx.clearRect(0, 0, canvaswidth, canvasheight); // limpa o canvas

  try {
    // Cria uma função baseada na fórmula digitada
    function formulaFunc(inputedValue) {
      const expr = math.compile(formula.value);
      let x = inputedValue / 10;
      let evaluate = expr.evaluate({ x });
      let result = evaluate * 10 - 36;
      return result;
    }

    // Shadow
    ctx.shadowColor = "rgba(80,80,80)";
    ctx.shadowBlur = 50;
    ctx.shadowOffsetX = 50;
    ctx.shadowOffsetY = 0;

    // Inicia o caminho da curva azul
    ctx.beginPath();

    let hit = 120;

    function testColison(x) {
      let val = math.round(x / 10) * 10;
      return (
        formulaFunc(x) > tubes[val / 30] - 1 ||
        formulaFunc(x) < tubes[val / 30] - 11
      );
    }

    for (let x = 90; x > 0; x -= 30) {
      if (testColison(x + 2)) {
        hit = x + 1.8;
      } else if (testColison(x)) {
        hit = x + 0.2;
      }
    }

    correctGraph = hit >= 120;

    if (correctGraph) {
      ctx.strokeStyle = "blue";
    } else {
      ctx.strokeStyle = "red";
    }

    // Desenha a curva no intervalo de x = 0 até x = 12
    for (let x = 0; x <= hit; x += 0.5) {
      const y = formulaFunc(x);
      const canvasX = x * 50;
      const canvasY = 200 - y * 50; // ajusta Y para o centro vertical

      // Move o cursor ou desenha linha dependendo da posição
      if (x === 0) ctx.moveTo(canvasX, canvasY);
      else ctx.lineTo(canvasX, canvasY);
    }
    ctx.lineWidth = 10;
    ctx.stroke(); // finaliza o desenho da curva
  } catch (e) {
    let eString = e + "";
    if (eString.includes("Error: Undefined function")) {
      alert(
        `Operação desconhecida: ${eString.replace(
          "Error: Undefined function",
          ""
        )}`
      );
    }
  }

  let font = "100px Arial";

  for (let i = 0; i < 4000; i += 500) {
    ctx.shadowColor = "transparent";
    ctx.strokeStyle = "rgba(80,80,80,0.5)";
    ctx.beginPath();
    ctx.moveTo(0, i);
    ctx.lineTo(6000, i);
    ctx.stroke();
    ctx.font = font;
    ctx.fillStyle = "rgba(80,80,80,0.5)";
    ctx.fillText(i / 500 - 4, 20, i - 10);
  }

  for (let i = 0; i < 6000; i += 500) {
    ctx.shadowColor = "transparent";
    ctx.strokeStyle = "rgba(80,80,80,0.5)";
    ctx.beginPath();
    ctx.moveTo(i, 0);
    ctx.lineTo(i, 4000);
    ctx.stroke();
    ctx.font = font;
    ctx.fillStyle = "rgba(80,80,80,0.5)";
    ctx.fillText(i / 500, i + 20, 1990);
  }

  ctx.shadowColor = "rgba(80,80,80)";
  // Desenha os canos em verde
  ctx.fillStyle = "green";
  for (const pipe of pipes.value) {
    // Parte superior do cano
    ctx.fillRect(pipe.x * 50, 0, 100, 140 - pipe.yMin * 50);
    ctx.fillRect(pipe.x * 50 - 30, 130 - pipe.yMin * 50, 160, 70);
    // Parte inferior do cano
    ctx.fillRect(pipe.x * 50, 460 - pipe.yMax * 50, 100, 4000);
    ctx.fillRect(pipe.x * 50 - 30, 400 - pipe.yMax * 50, 160, 70);
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
  width: 85dvh;
  min-width: 50%;
  max-width: 85%;
  border-radius: 10px;
  background-color: #d9d9d9;
}
.mathInput {
  border-radius: 10px;
  width: 85dvh;
  min-width: 50%;
  max-width: 85%;
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
  margin-left: 72vmin;
  width: 3%;
  text-align: center;
  background-color: #adadad;
}
</style>
