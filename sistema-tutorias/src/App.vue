<script setup>
import HelloWorld from './components/HelloWorld.vue'  
import TheWelcome from './components/TheWelcome.vue'
import login from './components/login.vue'
import sidebar from './components/sidebar.vue'
import inicio from './components/inicio.vue'

</script>

<template>
  <login></login>

  <sidebar>
  <main class="contenido">
    <h1>Bienvenido/a</h1>
    <p>A Sistema de Tutorías</p>
  </main>
  </sidebar>

  <inicio>
    <main class="contenido">
      <h1>Bienvenido/a</h1>
      <p>Inicio</p>
    </main>
  </inicio>
</template>


<style>
.contenido {
  margin-left: 250px;
  padding: 2rem;
}
</style>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
