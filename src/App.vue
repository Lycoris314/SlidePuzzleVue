<script setup>

import TheHeader from "./components/TheHeader.vue"
import TheMain from "./components/TheMain.vue"

</script>

<template>
  <TheHeader />
  <main>
    <TheMain />
  </main>
</template>

<style scoped>
* {
  margin: 0;
}

main {
  padding: 10px;
}
</style>
