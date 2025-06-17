<template>
  <div class="min-h-screen" :class="{'grid md:grid-cols-2 max-md:grid-rows-[auto,1fr]': activeItem !== 'jogo'}">
    <Transition name="logo" mode="out-in">
      <LogoPage
          v-if="activeItem !== 'jogo'"
          class="max-md:row-start-1"
          key="logo"
      />
    </Transition>
    <NavBar @item-selected="handleItemSelected" class="max-md:row-start-2" />
  </div>
</template>

<script setup>
import {ref} from 'vue';
import NavBar from "@/components/nav-bar.vue";
import LogoPage from "@/components/logo-page.vue";

const activeItem = ref('sobre');

function handleItemSelected(item) {
  activeItem.value = item;
}
</script>

<style>
.logo-enter-active,
.logo-leave-active {
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.logo-enter-from {
  opacity: 0;
  transform: translateX(50px);
}

.logo-leave-to {
  opacity: 0;
  transform: translateX(-50px);
  position: absolute;
}

/* Garante que o container do logo não colapse durante a transição */
.min-h-screen > :first-child {
  min-width: 50vw;
}
</style>