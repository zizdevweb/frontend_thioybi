<script setup>
import Rapport from './components/Rapport.vue'
import Piece from './components/Piece.vue'
</script>

<template>

  <main class="container">
    <h3 class="display-6 alert alert-primary m-5 text-center">Thioy-Bi Version fullStack</h3>
    <div class="row">
      <div class="col border boder-1">
        <Rapport />
      </div>
      <div class="col border boder-1">
        <Piece />
      </div> 
    </div>
   
  </main>
</template>

<style scoped>

</style>
