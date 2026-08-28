<template>
  <div class="pagina">
    <div class="barra-rosa"></div>

    <div class="contenedor">
      <div class="encabezado">
        <div class="logo">
          <div class="logo-circulo"></div>
        </div>
        <div>
          <h1>Información de cuenta</h1>
          <p class="subtitulo">Completá los datos para acceder a la plataforma</p>
        </div>
      </div>

      <div class="columnas">
        <div class="columna">
          <label>Apodo / Usuario <span class="obligatorio">*</span></label>
          <input v-model="usuario" type="text" placeholder="Pepe1234" />

          <label>Contraseña <span class="obligatorio">*</span></label>
          <div class="campo-password">
            <input
              v-model="contrasena"
              :type="mostrarContrasena ? 'text' : 'password'"
              placeholder="••••••••"
            />
            <span class="ojo" @click="mostrarContrasena = !mostrarContrasena">
              <span v-if="mostrarContrasena">🙈</span>
              <span v-else>👁️</span>
            </span>
          </div>
          <p class="ayuda">Mínimo 8 caracteres. Usá letras, números y símbolos.</p>
        </div>

        <div class="columna">
          <h1 class="titulo-con-icono">
            <span class="icono">👤</span> Información académica
          </h1>

          <label>Rol <span class="obligatorio">*</span></label>
          <select v-model="rol">
            <option disabled value="">Seleccioná tu rol</option>
            <option v-for="(r, index) in roles" :key="index" :value="r">
              {{ r }}
            </option>
          </select>

          <label>Email institucional <span class="obligatorio">*</span></label>
          <input v-model="email" type="email" placeholder="pgomez@escuelasproa.edu.ar" />
          <p class="ayuda">Usá tu correo institucional.</p>

          <label>Confirmar contraseña <span class="obligatorio">*</span></label>
          <div class="campo-password">
            <input
              v-model="confirmarContrasena"
              :type="mostrarConfirmar ? 'text' : 'password'"
              placeholder="••••••••"
            />
            <span class="ojo" @click="mostrarConfirmar = !mostrarConfirmar">
              <span v-if="mostrarConfirmar">🙈</span>
              <span v-else>👁️</span>
            </span>
          </div>
          <p class="ayuda">Repetí tu contraseña.</p>
        </div>
      </div>

      <div class="separador"></div>

      <p v-if="mensajeError" class="mensaje error">
        {{ mensajeError }}
      </p>
      <p v-else-if="registroExitoso" class="mensaje ok">
        Cuenta creada correctamente.
      </p>

      <button @click="registrarse" :class="{ deshabilitado: !formularioCompleto }">
        Registrarse
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      usuario: '',
      contrasena: '',
      confirmarContrasena: '',
      email: '',
      rol: '',
      roles: ['Docente', 'Directora', 'Secretaria', 'Preceptora', 'Coordinadora'],
      mensajeError: '',
      registroExitoso: false,
      mostrarContrasena: false,
      mostrarConfirmar: false
    }
  },
  computed: {
    formularioCompleto() {
      return (
        this.usuario.trim() !== '' &&
        this.contrasena.trim() !== '' &&
        this.confirmarContrasena.trim() !== '' &&
        this.email.trim() !== '' &&
        this.rol !== ''
      )
    }
  },
  methods: {
    registrarse() {
      this.registroExitoso = false

      if (!this.formularioCompleto) {
        this.mensajeError = 'Completá todos los campos obligatorios.'
        return
      }

      if (this.contrasena.length < 8) {
        this.mensajeError = 'La contraseña debe tener mínimo 8 caracteres.'
        return
      }

      if (this.contrasena !== this.confirmarContrasena) {
        this.mensajeError = 'Las contraseñas no coinciden.'
        return
      }

      this.mensajeError = ''
      this.registroExitoso = true
    }
  }
}
</script>

<style>
body {
  margin: 0;
}
.pagina {
  display: flex;
  min-height: 100vh;
  font-family: Arial, sans-serif;
}
.barra-rosa {
  width: 12px;
  background-color: pink;
}
.contenedor {
  flex: 1;
  width: 95%;
  max-width: 1400px;
  margin: 40px auto;
  padding: 40px;
  border: 1px solid #ddd;
  border-radius: 10px;
}
.encabezado {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}
.logo-circulo {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: linear-gradient(to right, white 50%, #4a90d9 50%);
  border: 2px solid #333;
  position: relative;
}
.logo-circulo::after {
  content: '';
  position: absolute;
  top: 32%;
  left: 18%;
  width: 12px;
  height: 12px;
  border-radius: 50%;
  background-color: #4a90d9;
}
h1 {
  font-size: 22px;
  margin: 0 0 4px 0;
}
.subtitulo {
  color: gray;
  font-size: 14px;
  margin: 0;
}
.columnas {
  display: flex;
  gap: 80px;
  flex-wrap: wrap;
}
.columna {
  flex: 1;
  min-width: 280px;
}
.titulo-con-icono {
  display: flex;
  align-items: center;
  gap: 8px;
}
.icono {
  font-size: 20px;
}
label {
  display: block;
  font-weight: bold;
  margin-bottom: 4px;
  margin-top: 12px;
}
.obligatorio {
  color: red;
}
input, select {
  display: block;
  width: 100%;
  margin-bottom: 4px;
  padding: 10px;
  border: 1px solid #999;
  border-radius: 4px;
  box-sizing: border-box;
  font-size: 14px;
}
.campo-password {
  position: relative;
}
.campo-password input {
  padding-right: 36px;
}
.ojo {
  position: absolute;
  right: 10px;
  top: 8px;
  cursor: pointer;
  font-size: 16px;
}
.ayuda {
  font-size: 12px;
  color: gray;
  margin-top: 0;
  margin-bottom: 8px;
}
.separador {
  height: 4px;
  background-color: pink;
  border-radius: 2px;
  margin: 24px 0;
}
.mensaje.error {
  color: red;
  font-weight: bold;
}
.mensaje.ok {
  color: green;
  font-weight: bold;
}
button {
  width: 100%;
  padding: 12px;
  background-color: #4a90d9;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 15px;
  font-weight: bold;
  cursor: pointer;
  margin-top: 10px;
}
.deshabilitado {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>