<template>
  <div class="flex flex-col p-4 md:p-8">
    <!-- Menu de Navegação -->
    <div class="border-b border-gray-200 pb-4 mb-6">
      <nav class="flex justify-center gap-4 md:gap-8">
        <button
            v-for="(item, index) in menuItems"
            :key="index"
            @click="handleMenuClick(item.target)"
            class="px-4 py-2 transition-all"
            :class="{
            'font-bold text-purple-900 border-b-2 border-purple-900': activeSection === item.target,
            'hover:text-purple-700': activeSection !== item.target
          }"
        >
          {{ item.label }}
        </button>
      </nav>
    </div>

    <!-- Menu/barra de navegação -->
    <div class="flex-1 overflow-auto">
      <About v-if="showAbout" id="sobre" />
      <PipeGame v-if="showGame" id="jogo" />
      <HowPlay v-if="showHowPlay" id="howPlay" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import PipeGame from "@/components/PipeGame.vue";
import About from "@/components/about.vue";
import HowPlay from "@/components/how-play.vue";

const menuItems = [
  {label: 'Sobre', target: 'sobre'},
  {label: 'Jogo', target: 'jogo'},
  {label: 'Como Jogar', target: 'howPlay'},
];

const activeSection = ref('sobre');
const showAbout = ref(true);
const showGame = ref(false);
const showHowPlay = ref(false);

function handleMenuClick(target) {
  activeSection.value = target;

  // Atualiza visibilidade do menu
  showAbout.value = target === 'sobre';
  showGame.value = target === 'jogo';
  showHowPlay.value = target === 'howPlay';

  // Scroll suave
  const el = document.getElementById(target);
  if (el) el.scrollIntoView({behavior: 'smooth'});
}


onMounted(() => {
  const el = document.getElementById('sobre');
  if (el) el.scrollIntoView({behavior: 'auto'});
});
</script>