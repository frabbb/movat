<script setup>
import Canvas from "./components/Canvas.vue";
</script>

<template>
  <div>
    <img src="./assets/logo.png" alt="" class="logo" />

    <div class="date">
      <h1>30-31</h1>
      <h2>Agosto</h2>

      <div class="chips">
        <div class="chip">
          <h3>Viale Facsal, Piacenza</h3>
        </div>
      </div>
    </div>
    <div class="container">
      <Canvas />
    </div>
  </div>
</template>

<style scoped>
.logo {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translate(-50%, 0);
  width: min(50vw, 120px);
}

.chips {
  display: flex;
  gap: 10px;
  justify-content: center;
  align-items: center;
  margin-top: 10px;
}

.date {
  position: fixed;
  top: 50%;
  right: 50%;
  transform: translate(50%, -50%);
  width: 1000%;
}
</style>
