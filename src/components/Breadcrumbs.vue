<script setup>
const breadcrumbs = [
  { name: "Главная", link: "/" },
  { name: "Системы хранения", link: "#" },
  { name: "Комплекты стеллажных систем", link: null },
];
</script>

<template>
  <nav class="breadcrumbs">
    <template v-for="(crumb, index) in breadcrumbs" :key="index">
      <span v-if="index !== 0" class="breadcrumbs__separator">/</span>
      <a v-if="crumb.link" :href="crumb.link" class="breadcrumbs__item breadcrumbs__link">{{ crumb.name }}</a>
      <span v-else class="breadcrumbs__item breadcrumbs__current">{{ crumb.name }}</span>
    </template>
  </nav>
</template>
