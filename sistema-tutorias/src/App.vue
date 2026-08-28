<template>
  <div class="pantalla">
    <div class="tarjeta">
      <div class="logo">
        <span class="logo-icono">🗂️</span>
        <div>
          <h2 class="logo-titulo">TUTO Script</h2>
          <p class="logo-subtitulo">Gestión de tutorías escolares</p>
        </div>
      </div>

      <div class="icono-candado">🔒</div>

      <h1 class="titulo">Recuperar Contraseña</h1>
      <p class="texto">
        Ingresá tu correo electrónico o usuario y te enviaremos un enlace
        para restablecer tu contraseña.
      </p>

      <label class="etiqueta">Correo electrónico o usuario</label>
      <input
        v-model="correoUsuario"
        type="text"
        placeholder="Correo electrónico o usuario"
        class="campo"
      />

      <p v-if="mensajeError" class="mensaje error">
        {{ mensajeError }}
      </p>
      <p v-else-if="enlaceEnviado" class="mensaje ok">
        Enlace de recuperación enviado. Revisá tu correo.
      </p>

      <button
        @click="enviarEnlace"
        :class="{ boton: true, deshabilitado: correoUsuario.trim() === '' }"
      >
        Enviar enlace de Recuperación
      </button>

      <a href="#" class="volver">← Volver al inicio de sesión</a>
    </div>
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
html, body {
  margin: 0;
  padding: 0;
}

.pantalla {
  min-height: 100vh;
  width: 100vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #f4f6f4;
  font-family: Arial, sans-serif;
  box-sizing: border-box;
}

.tarjeta {
  width: 90%;
  max-width: 1100px;
  padding: 4vw 6vw;
  background-color: white;
  border: 2px solid #2e6b3e;
  border-radius: 12px;
  text-align: center;
  box-shadow: 4px 4px 0px rgba(46, 107, 62, 0.15);
  box-sizing: border-box;
}

.logo {
  display: flex;
  align-items: center;
  gap: 14px;
  margin-bottom: 30px;
  text-align: left;
}

.logo-icono {
  font-size: 38px;
}

.logo-titulo {
  margin: 0;
  font-size: 24px;
  color: #2e6b3e;
}

.logo-subtitulo {
  margin: 2px 0 0 0;
  font-size: 14px;
  color: #777;
}

.icono-candado {
  font-size: 56px;
  margin: 10px 0 8px 0;
}

.titulo {
  font-size: 30px;
  margin-bottom: 14px;
  color: #222;
}

.texto {
  font-size: 16px;
  color: #555;
  line-height: 1.5;
  margin: 0 auto 28px auto;
  max-width: 500px;
}

.etiqueta {
  display: block;
  text-align: left;
  font-size: 14px;
  font-weight: bold;
  margin-bottom: 8px;
  color: #333;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.campo {
  display: block;
  width: 100%;
  max-width: 500px;
  padding: 14px;
  margin: 0 auto 18px auto;
  box-sizing: border-box;
  border: 1px solid #999;
  border-radius: 6px;
  font-size: 15px;
}

.boton {
  width: 100%;
  max-width: 500px;
  padding: 16px;
  background-color: #8fbf9f;
  border: 1px solid #2e6b3e;
  border-radius: 6px;
  font-weight: bold;
  font-size: 16px;
  color: #1c3d24;
  cursor: pointer;
  margin: 0 auto 20px auto;
  display: block;
  transition: background-color 0.2s;
}

.boton:hover {
  background-color: #7bab8b;
}

.deshabilitado {
  opacity: 0.5;
  cursor: not-allowed;
}

.mensaje {
  font-size: 14px;
  margin-bottom: 12px;
}

.mensaje.error {
  color: #c0392b;
}

.mensaje.ok {
  color: #2e6b3e;
}

.volver {
  display: block;
  font-size: 14px;
  color: #2255cc;
  text-decoration: none;
}

.volver:hover {
  text-decoration: underline;
}
</style>