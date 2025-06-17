<script setup lang="ts">

import TextStructure from "@/components/text-structure.vue";
</script>

<template>
  <div class="flex flex-col justify-center items-start">
<span class="text-2xl self-center pt-4">Como Jogar</span>
    <div class=" flex flex-col w-[500px] self-center pt-4 gap-y-2 text-justify">
<text-structure title="1." content=" A linha será desenhada automaticamente no plano cartesiano

Assim que você escrever a função, a linha aparecerá no gráfico. O desafio é fazer essa linha passar entre os espaços dos obstáculos.

→ Sua função deve ser escrita em termos de x.  Exemplo:2*x-4"></text-structure>
<text-structure title="2." content=" Desvie dos obstáculos: a função será automaticamente desenhada no plano cartesiano do jogo. Tente escrever uma função que faça a linha passar entre os canos sem tocá-los. Você pode usar →
Funções básicas: x, x^2, 1/x
Funções trigonométricas: sin(x), cos(x)
Funções compostas: 2*sin(x) + x/3"></text-structure>
<text-structure title="3." content="Dicas úteis: sin(x) e cos(x) são ótimos para criar curvas onduladas.

abs(x) cria reflexos no eixo y.

Funções do tipo x^2, x^3 formam parábolas e curvas suaves.

."></text-structure>
<text-structure title="4." content="Desafie-se: tente ajustar a função para que a linha passe exatamente pelos espaços entre os canos, sem tocá-los."></text-structure>
  </div>
  </div>
</template>

<style scoped>

</style>