<script setup>
import Login from './components/login.vue'
import Sidebar from './components/sidebar.vue'
import Inicio from './components/inicio.vue'
</script>

<template>
  <div class="app">

    <Login />

    <div class="layout">
      <Sidebar />

      <main class="contenido">
        <Inicio />
      </main>
    </div>

  </div>
</template>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
}

html,
body,
#app {
  margin: 0;
  padding: 0;
  width: 100%;
  min-width: 0;
  min-height: 100vh;
}

body {
  overflow-x: hidden;
}

.app {
  width: 100%;
  min-width: 0;
}

/* Layout general */
.layout {
  width: 100%;
  min-width: 0;
  min-height: 100vh;

  display: flex;
  align-items: stretch;
}

/* Contenido situado al lado de la sidebar */
.contenido {
  flex: 1 1 auto;
  min-width: 0;
  width: 0;

  padding: clamp(1rem, 3vw, 2rem);
}

/* Evita que el contenido rompa el ancho */
.contenido > * {
  max-width: 100%;
}

/* Pantallas estrechas */
@media (max-width: 700px) {
  .layout {
    flex-direction: column;
  }

  .contenido {
    width: 100%;
  }
}
</style>