<template>
  <!-- Mostra o grid apenas se não for a tela de jogo -->
  <div v-if="!isGameView" class="grid md:grid-cols-2 max-md:grid-rows-[auto_1fr] min-h-screen">
    <!-- Banner Inicial -->
    <div
        class="bg-[#26C4A5] flex flex-col items-center justify-center gap-4 p-8 text-white text-2xl"
    >
      <img class="w-72" src="@/assets/GroupLogoFinal.svg" alt="Logo Flappy Graphic">
      <span>Construa seu conhecimento</span>
    </div>

    <!-- NavBar -->
    <NavBar
        @item-selected="handleItemSelected"
        @game-selected="handleGameSelected"
    />
  </div>

  <!-- vou mudar pra atualizar a visibilidade da logo quando estiver no jogo-->
  <NavBar
      v-else
      @item-selected="handleItemSelected"
      @game-selected="handleGameSelected"
  />
</template>

<script setup>
import { ref } from 'vue';
import NavBar from "@/components/nav-bar.vue";

const hasSelectedItem = ref(false);
const isGameView = ref(false);

function handleItemSelected() {
  hasSelectedItem.value = true;
  isGameView.value = false; // Reset ao selecionar outros itens
}

function handleGameSelected() {
  hasSelectedItem.value = true;
  isGameView.value = true; // Ativa o modo "somente jogo"
}
</script>