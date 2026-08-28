<template>
  <div class="pagina">
    <!-- Panel de marca -->
    <aside class="panel-marca">
      <div class="patron-puntos"></div>
      <div class="marca-contenido">
        <div class="logo">
          <div class="logo-circulo"></div>
          <span class="logo-texto">TUTORIAS</span>
        </div>
        <h2 class="marca-titulo">Sumate a la plataforma</h2>
        <p class="marca-texto">Gestión de tutorías escolares</p>
      </div>
    </aside>

    <!-- Formulario -->
    <main class="panel-formulario">
      <div class="contenedor">
        <div class="encabezado">
          <h1>Información de cuenta</h1>
          <p class="subtitulo">Completá los datos para acceder a la plataforma</p>
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
          <span class="mensaje-icono">⚠</span> {{ mensajeError }}
        </p>
        <p v-else-if="registroExitoso" class="mensaje ok">
          <span class="mensaje-icono">✔</span> Cuenta creada correctamente.
        </p>

        <button @click="registrarse" :class="{ deshabilitado: !formularioCompleto }">
          Registrarse
        </button>
      </div>
    </main>
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
@import url('https://fonts.googleapis.com/css2?family=Lora:wght@500;600;700&family=Inter:wght@400;500;600;700&display=swap');

* {
  box-sizing: border-box;
}

body {
  margin: 0;
}

:root {
  --tinta: #22301f;
  --verde: #3a6b4a;
  --verde-oscuro: #1c3a28;
  --verde-suave: #e7efe6;
  --fondo: #f2f1e9;
  --borde: #e2e0d4;
  --dorado: #a9821f;
  --rosa: #c97b84;
  --rosa-suave: #f7e8ea;
  --rojo: #b3413a;
  --texto-mudo: #7c7a6f;
}

.pagina {
  display: flex;
  flex-direction: row;
  flex-wrap: nowrap;
  min-height: 100vh;
  min-width: 1100px;
  font-family: 'Inter', Arial, sans-serif;
  background-color: var(--fondo);
  color: var(--tinta);
}

/* Panel de marca: ancho fijo, pensado para escritorio */
.panel-marca {
  position: relative;
  width: 420px;
  flex-shrink: 0;
  background: linear-gradient(160deg, var(--verde-oscuro), var(--verde));
  color: #ffffff;
  display: flex;
  align-items: center;
  overflow: hidden;
}

.patron-puntos {
  position: absolute;
  inset: 0;
  background-image: radial-gradient(rgba(255, 255, 255, 0.12) 1.5px, transparent 1.5px);
  background-size: 26px 26px;
  opacity: 0.7;
}

.marca-contenido {
  position: relative;
  z-index: 1;
  padding: 56px;
}

.logo {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 60px;
}

.logo-circulo {
  width: 34px;
  height: 34px;
  border-radius: 50%;
  background: linear-gradient(to right, #ffffff 50%, var(--dorado) 50%);
  border: 2px solid rgba(255, 255, 255, 0.85);
  position: relative;
  flex-shrink: 0;
}

.logo-circulo::after {
  content: '';
  position: absolute;
  top: 32%;
  left: 18%;
  width: 9px;
  height: 9px;
  border-radius: 50%;
  background-color: var(--dorado);
}

.logo-texto {
  font-family: 'Lora', serif;
  font-size: 16px;
  font-weight: 600;
  letter-spacing: 1.5px;
}

.marca-titulo {
  font-family: 'Lora', serif;
  font-size: 34px;
  font-weight: 600;
  line-height: 1.3;
  margin: 0 0 14px 0;
  max-width: 320px;
}

.marca-texto {
  font-size: 14.5px;
  color: rgba(255, 255, 255, 0.78);
  margin: 0;
}

/* Panel del formulario */
.panel-formulario {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 40px;
}

.contenedor {
  width: 100%;
  max-width: 900px;
  background-color: #ffffff;
  padding: 48px;
  border: 1px solid var(--borde);
  border-radius: 16px;
  box-shadow: 0 10px 40px rgba(34, 48, 31, 0.06);
}

.encabezado {
  margin-bottom: 28px;
  padding-bottom: 20px;
  border-bottom: 1px dashed var(--borde);
}

h1 {
  font-family: 'Lora', serif;
  font-size: 22px;
  font-weight: 600;
  color: var(--tinta);
  margin: 0 0 6px 0;
}

.subtitulo {
  color: var(--texto-mudo);
  font-size: 14px;
  margin: 0;
}

.columnas {
  display: flex;
  gap: 56px;
  flex-wrap: wrap;
}

.columna {
  flex: 1;
  min-width: 260px;
}

.titulo-con-icono {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 15px;
  color: var(--verde-oscuro);
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 18px;
  font-family: 'Inter', sans-serif;
  font-weight: 700;
}

.icono {
  font-size: 17px;
}

label {
  display: block;
  font-size: 12px;
  font-weight: 600;
  color: #4a4a42;
  margin-bottom: 6px;
  margin-top: 16px;
  text-transform: uppercase;
  letter-spacing: 0.3px;
}

.obligatorio {
  color: var(--rojo);
}

input,
select {
  display: block;
  width: 100%;
  margin-bottom: 4px;
  padding: 11px 13px;
  border: 1.5px solid var(--borde);
  border-radius: 8px;
  box-sizing: border-box;
  font-family: 'Inter', sans-serif;
  font-size: 14px;
  color: var(--tinta);
  background-color: #fdfdfb;
  transition: border-color 0.2s, box-shadow 0.2s, background-color 0.2s;
}

input::placeholder {
  color: #b7b5a8;
}

input:focus,
select:focus {
  outline: none;
  border-color: var(--verde);
  background-color: #ffffff;
  box-shadow: 0 0 0 3px rgba(58, 107, 74, 0.12);
}

select {
  appearance: none;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='%237c7a6f' stroke-width='2'%3E%3Cpath d='M6 9l6 6 6-6'/%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: right 12px center;
  background-size: 16px;
  padding-right: 36px;
}

.campo-password {
  position: relative;
}

.campo-password input {
  padding-right: 40px;
}

.ojo {
  position: absolute;
  right: 12px;
  top: 10px;
  cursor: pointer;
  font-size: 15px;
  opacity: 0.7;
  transition: opacity 0.2s;
}

.ojo:hover {
  opacity: 1;
}

.ayuda {
  font-size: 12px;
  color: var(--texto-mudo);
  margin-top: 0;
  margin-bottom: 8px;
}

.separador {
  height: 1px;
  background-color: var(--borde);
  margin: 28px 0 20px 0;
}

.mensaje {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 13.5px;
  font-weight: 600;
  padding: 11px 16px;
  border-radius: 8px;
  margin-bottom: 16px;
}

.mensaje.error {
  color: var(--rojo);
  background-color: var(--rosa-suave);
  border: 1px solid #edc9cc;
}

.mensaje.ok {
  color: var(--verde-oscuro);
  background-color: var(--verde-suave);
  border: 1px solid #cfe0cf;
}

.mensaje-icono {
  font-size: 14px;
}

button {
  width: 100%;
  padding: 14px;
  background-color: var(--verde);
  color: white;
  border: none;
  border-radius: 8px;
  font-family: 'Inter', sans-serif;
  font-size: 14.5px;
  font-weight: 700;
  letter-spacing: 0.2px;
  cursor: pointer;
  margin-top: 6px;
  box-shadow: 0 4px 14px rgba(28, 58, 40, 0.25);
  transition: background-color 0.2s, transform 0.15s, box-shadow 0.2s;
}

button:hover:not(.deshabilitado) {
  background-color: var(--verde-oscuro);
  transform: translateY(-1px);
  box-shadow: 0 6px 18px rgba(28, 58, 40, 0.3);
}

.deshabilitado {
  opacity: 0.5;
  cursor: not-allowed;
  box-shadow: none;
}
</style>