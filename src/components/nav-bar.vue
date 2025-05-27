<template>
  <div class="w-[600px] border-b flex flex-col items-center justify-center gap-2">
    <nav class="flex gap-x-40 p-4">
      <button
          v-for="(item, index) in menuItems"
          :key="index"
          @click="handleMenuClick(item.target)"
          :class="{'font-bold underline decoration-2 decoration-purple-900': activeSection === item.target}"
      >
        {{ item.label }}
      </button>
    </nav>
  </div>

  <section v-if="showGame" id="jogo">
    <span class="text-2xl flex justify-center font-bold mb-4">Flappy Graphic</span>
    <PipeGame />
  </section>

  <section v-if="showHowPlay" id="howPlay" class="flex flex-col justify-center items-center">
    <HowPlay />
  </section>

  <section v-if="showAbout" id="sobre" class="flex flex-col justify-center items-center">
    <About />
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import PipeGame from "@/components/PipeGame.vue";
import About from "@/components/about.vue";
import HowPlay from "@/components/icons/how-play.vue";

const menuItems = [
  { label: 'Jogo', target: 'jogo' },
  { label: 'Como Jogar', target: 'howPlay' },
  { label: 'Sobre', target: 'sobre' },
];

// ✅ Inicia com JOGO ativo
const activeSection = ref('jogo');

// ✅ Controle de visibilidade inicial
const showGame = ref(true);
const showHowPlay = ref(true);
const showAbout = ref(false);

function handleMenuClick(target) {
  activeSection.value = target;

  if (target === 'sobre') {
    showAbout.value = true;
    showGame.value = false;
    showHowPlay.value = false;
  } else {
    showAbout.value = false;
    showGame.value = true;
    showHowPlay.value = true;
  }

  // Scroll suave para a seção
  const el = document.getElementById(target);
  if (el) {
    el.scrollIntoView({ behavior: 'smooth' });
  }
}

// ✅ Opcional: garantir que o scroll inicie na seção "Jogo"
onMounted(() => {
  const el = document.getElementById('jogo');
  if (el) {
    el.scrollIntoView({ behavior: 'auto' });
  }
});
</script>

<style scoped>
button {
  cursor: pointer;
}
</style>
