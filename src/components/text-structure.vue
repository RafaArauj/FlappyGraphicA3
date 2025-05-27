<!-- componente reutilizável com estrutura de de texto de titulo - conteúdo-->
<script setup lang="ts">
const props = defineProps<{
// define campos de título e conteúdo e pega essas informações do componente pai (how-play.vue) e passa para o componente filho (text-structure.vue)
  title: string,
  content: string,
}>();
</script>

<template>
  <div>
<!--    -->
    <span class="font-bold">{{title}}</span>
    <span> {{content}}</span>
  </div>
</template>

<style scoped>

</style>