<template>
  <div class="contenedor">
    <div class="logo">
      <h2>TUTO Script</h2>
      <p class="subtitulo">Gestión de tutorías escolares</p>
    </div>

    <div class="icono">🔒</div>

    <h1>Recuperar Contraseña</h1>
    <p class="texto">
      Ingresá tu correo electrónico o usuario y te enviaremos un enlace
      para restablecer tu contraseña.
    </p>

    <label>Correo electrónico o usuario</label>
    <input v-model="correoUsuario" type="text" placeholder="Correo electrónico o usuario" />

    <p v-if="mensajeError" class="mensaje error">
      {{ mensajeError }}
    </p>
    <p v-else-if="enlaceEnviado" class="mensaje ok">
      Enlace de recuperación enviado. Revisá tu correo.
    </p>

    <button
      @click="enviarEnlace"
      :class="{ deshabilitado: correoUsuario.trim() === '' }"
    >
      Enviar enlace de recuperación
    </button>

    <a href="#" class="volver">← Volver al inicio de sesión</a>
  </div>
</template>

<script>
export default {
  data() {
    return {
      correoUsuario: '',
      mensajeError: '',
      enlaceEnviado: false
    }
  },
  methods: {
    enviarEnlace() {
      this.enlaceEnviado = false

      if (this.correoUsuario.trim() === '') {
        this.mensajeError = 'Ingresá tu correo electrónico o usuario.'
        return
      }

      this.mensajeError = ''
      this.enlaceEnviado = true
    }
  }
}
</script>

<style>
.contenedor {
  max-width: 420px;
  margin: 60px auto;
  padding: 24px;
  font-family: Arial, sans-serif;
  text-align: center;
  border: 1px solid #ccc;
  border-radius: 8px;
}
.logo h2 {
  margin-bottom: 0;
  color: darkgreen;
}
.subtitulo {
  margin-top: 4px;
  font-size: 13px;
  color: gray;
}
.icono {
  font-size: 40px;
  margin: 16px 0;
}
.texto {
  font-size: 14px;
  color: #444;
  margin-bottom: 16px;
}
label {
  display: block;
  text-align: left;
  margin-bottom: 4px;
  font-weight: bold;
}
input {
  display: block;
  width: 100%;
  margin-bottom: 12px;
  padding: 8px;
  box-sizing: border-box;
}
button {
  width: 100%;
  padding: 10px;
  background-color: darkseagreen;
  border: 1px solid #2e6b2e;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
}
.deshabilitado {
  opacity: 0.5;
}
.mensaje.error {
  color: red;
}
.mensaje.ok {
  color: green;
}
.volver {
  display: block;
  margin-top: 16px;
  color: #3366cc;
  text-decoration: none;
  font-size: 14px;
}
</style>