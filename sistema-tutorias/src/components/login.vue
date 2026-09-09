<template>
  <div class="login-page">

    <header class="header">
      <div class="brand">
        <div class="logo">
          <span class="logo-person purple"></span>
          <span class="logo-person yellow"></span>
        </div>

        <div>
          <p class="brand-name">Tuto Script</p>
          <p class="brand-description">Gestión de tutorías escolares</p>
        </div>
      </div>

      <nav class="navigation">
        <a href="#funciones">Funciones</a>
        <a href="#contacto">Contacto</a>
      </nav>
    </header>

    <main class="main-content">

      <section class="presentation" id="funciones">
        <span class="eyebrow">
          Hecho para tutores y equipos de acompañamiento
        </span>

        <h1>
          Organizá,<br>
          acompañá,<br>
          transformá.
        </h1>

        <p class="description">
          Tuto Script centraliza el seguimiento de tutorías escolares
          para que cada estudiante tenga el acompañamiento que necesita,
          sin planillas sueltas ni seguimientos perdidos.
        </p>

        <div class="features">

          <div class="feature">
            <div class="feature-icon purple">
              ✓
            </div>
            <span>Gestión de tutorías</span>
          </div>

          <div class="feature">
            <div class="feature-icon yellow">
              !
            </div>
            <span>Notificaciones automáticas</span>
          </div>

          <div class="feature">
            <div class="feature-icon blue">
              ↗
            </div>
            <span>Seguimiento académico</span>
          </div>

          <div class="feature">
            <div class="feature-icon green">
              ▤
            </div>
            <span>Reportes personalizados</span>
          </div>

        </div>
      </section>

      <section class="login-card">

        <div class="login-header">
          <h2>Iniciar sesión</h2>
          <p>Ingresá tus datos para continuar</p>
        </div>

        <form @submit.prevent="handleSubmit">

          <div class="input-group">
            <label for="email">
              Correo o usuario
            </label>

            <input
              id="email"
              v-model="form.email"
              type="text"
              autocomplete="username"
              placeholder="nombre@escuela.edu.ar"
              required
            >
          </div>

          <div class="input-group">
            <label for="password">
              Contraseña
            </label>

            <div class="password-container">
              <input
                id="password"
                v-model="form.password"
                :type="showPassword ? 'text' : 'password'"
                autocomplete="current-password"
                placeholder="Tu contraseña"
                required
              >

              <button
                type="button"
                class="show-password"
                @click="showPassword = !showPassword"
              >
                {{ showPassword ? 'Ocultar' : 'Mostrar' }}
              </button>
            </div>
          </div>

          <a
            href="#"
            class="forgot-password"
            @click.prevent
          >
            ¿Olvidaste tu contraseña?
          </a>

          <label class="remember">
            <input
              v-model="form.remember"
              type="checkbox"
            >

            <span>Recordar usuario</span>
          </label>

          <button
            type="submit"
            class="login-button"
          >
            Iniciar sesión
            <span>→</span>
          </button>

          <p
            v-if="statusMessage"
            class="status-message"
          >
            {{ statusMessage }}
          </p>

          <div class="separator"></div>

          <p class="register">
            ¿No tenés cuenta?
            <a href="#" @click.prevent>
              Registrarse
            </a>
          </p>

        </form>
      </section>

    </main>

    <div class="landscape" aria-hidden="true">
      <div class="hill hill-back"></div>
      <div class="hill hill-front"></div>

      <div class="launch">
        <div class="rocket"></div>
      </div>
    </div>

    <footer class="footer" id="contacto">
      <span>📞 03548 123456</span>
      <span>✉️ contacto@tutoscript.edu.ar</span>
      <span>📍 La Falda, Córdoba</span>
    </footer>

  </div>
</template>

<script setup>
import { reactive, ref } from 'vue'

const showPassword = ref(false)
const statusMessage = ref('')

const form = reactive({
  email: '',
  password: '',
  remember: false
})

function handleSubmit() {
  statusMessage.value =
    'Esto es una vista de demostración — no hay backend conectado todavía.'
}
</script>

<style scoped>

:global(*) {
  box-sizing: border-box;
}

:global(html),
:global(body),
:global(#app) {
  margin: 0;
  min-height: 100%;
}

.login-page {
  min-height: 100vh;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
  overflow-x: hidden;
}

/* HEADER */

.header {
  max-width: 1160px;
  height: 92px;
  margin: 0 auto;
  padding: 0 20px;

  display: flex;
  align-items: center;
  justify-content: space-between;
}

.brand {
  display: flex;
  align-items: center;
  gap: 14px;
}

.logo {
  width: 52px;
  height: 52px;

  display: flex;
  align-items: center;
  justify-content: center;

  position: relative;

  border: 2px solid #231f2c;
  border-radius: 14px;
  background: #fffdf9;
}

.logo-person {
  position: absolute;
  bottom: 9px;

  width: 17px;
  height: 17px;

  border-radius: 50%;
}

.logo-person::after {
  content: "";
  position: absolute;

  width: 25px;
  height: 14px;

  left: 50%;
  bottom: -13px;
  transform: translateX(-50%);

  border-radius: 14px 14px 0 0;
}

.logo-person.purple {
  left: 9px;
  background: #6b9a55;
}

.logo-person.purple::after {
  background: #6b9a55;
}

.logo-person.yellow {
  right: 9px;
  background: #e5b54c;
}

.logo-person.yellow::after {
  background: #e5b54c;
}

.brand-name {
  margin: 0;
  font-size: 26px;
  font-weight: 700;
}

.brand-description {
  margin: 3px 0 0;
  color: #6b6577;
  font-size: 14px;
}

.navigation {
  display: flex;
  gap: 30px;
}

.navigation a {
  color: #6b6577;
  text-decoration: none;
  font-size: 14px;
  font-weight: 600;
}

/* MAIN */

.main-content {
  max-width: 1160px;
  margin: 0 auto;
  padding: 54px 20px 0;

  display: grid;
  grid-template-columns: 1fr 430px;
  gap: 70px;
}

.presentation {
  padding-top: 5px;
}

.eyebrow {
  display: inline-block;

  padding: 7px 15px;

  background: #f4eefb;
  border-radius: 999px;

  color: #6c4b9c;
  font-size: 13px;
  font-weight: 600;
}

.presentation h1 {
  margin: 20px 0 0;

  font-size: 62px;
  line-height: 1.04;
  letter-spacing: -2px;

  font-style: italic;
}

.description {
  max-width: 520px;
  margin: 22px 0 0;

  color: #6b6577;
  font-size: 16px;
  line-height: 1.65;
}

/* FEATURES */

.features {
  margin-top: 30px;

  display: flex;
  flex-direction: column;
  gap: 15px;
}

.feature {
  display: flex;
  align-items: center;
  gap: 14px;

  font-size: 16px;
  font-weight: 600;
}

.feature-icon {
  width: 39px;
  height: 39px;

  display: flex;
  align-items: center;
  justify-content: center;

  flex-shrink: 0;

  border: 1.5px solid #231f2c;
  border-radius: 50%;

  font-weight: 700;
}

.feature-icon.purple {
  background: #f4eefb;
}

.feature-icon.yellow {
  background: #faebc7;
}

.feature-icon.blue {
  background: #dceef9;
}

.feature-icon.green {
  background: #e2eeda;
}

/* LOGIN */

.login-card {
  width: 100%;

  padding: 34px;

  background: #fffdf9;

  border: 2px solid #231f2c;
  border-radius: 24px;
}

.login-header h2 {
  margin: 0;
  font-size: 26px;
  font-style: italic;
}

.login-header p {
  margin: 7px 0 0;

  color: #6b6577;
  font-size: 14px;
}

/* INPUTS */

.input-group {
  margin-top: 22px;
}

.input-group label {
  display: block;

  margin-bottom: 8px;

  font-size: 13px;
  font-weight: 700;
}

.input-group input {
  width: 100%;
  height: 46px;

  padding: 0 14px;

  border: 1.5px solid #231f2c;
  border-radius: 10px;

  background: #fffdf9;
  color: #231f2c;

  font-family: inherit;
  font-size: 15px;
  outline: none;
}

.input-group input:focus {
  background: white;
}

.input-group input::placeholder {
  color: #aaa5ad;
}

/* PASSWORD */

.password-container {
  display: flex;
  gap: 8px;
}

.password-container input {
  flex: 1;
}

.show-password {
  height: 46px;

  padding: 0 13px;

  border: 1.5px solid #231f2c;
  border-radius: 10px;

  background: #fffdf9;
  color: #231f2c;

  font-size: 13px;
  font-weight: 600;

  cursor: pointer;
}

/* FORGOT */

.forgot-password {
  display: block;

  margin-top: 9px;

  text-align: right;

  color: #6b9a55;

  text-decoration: none;

  font-size: 13px;
  font-weight: 600;
}

/* REMEMBER */

.remember {
  display: flex;
  align-items: center;
  gap: 8px;

  margin-top: 20px;

  color: #6b6577;

  font-size: 14px;
  cursor: pointer;
}

.remember input {
  width: 16px;
  height: 16px;
}

/* BUTTON */

.login-button {
  width: 100%;
  height: 50px;

  margin-top: 24px;

  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;

  border: 1.5px solid #231f2c;
  border-radius: 10px;

  background: #4e9cd4;
  color: #231f2c;

  font-size: 15px;
  font-weight: 700;

  cursor: pointer;
}

.login-button span {
  font-size: 19px;
}

/* STATUS */

.status-message {
  margin: 12px 0 0;

  text-align: center;

  color: #6c4b9c;

  font-size: 13px;
  font-weight: 600;
  line-height: 1.4;
}

.separator {
  height: 1px;
  margin: 24px 0 17px;
  background: #e7e0d3;
}

.register {
  margin: 0;

  text-align: center;

  color: #6b6577;
  font-size: 14px;
}

.register a {
  color: #4e9cd4;
  font-weight: 700;
  text-decoration: none;
}

/* LANDSCAPE */

.landscape {
  position: relative;

  height: 220px;
  margin-top: 55px;

  overflow: hidden;
}

.hill {
  position: absolute;
  left: -5%;
  width: 110%;
  border-top: 2px solid #231f2c;
  border-radius: 50%;
}

.hill-back {
  bottom: 25px;
  height: 170px;
  background: #e7ddf6;
  transform: rotate(-2deg);
}

.hill-front {
  bottom: -70px;
  height: 170px;
  background: #faf5ec;
  transform: rotate(2deg);
}

.launch {
  position: absolute;

  left: 50%;
  bottom: 8px;
  transform: translateX(-50%);

  width: 52px;
  height: 42px;

  border: 2px solid #231f2c;
  border-radius: 6px;

  background: #faebc7;
}

.rocket {
  position: absolute;

  left: 50%;
  bottom: 39px;

  width: 8px;
  height: 55px;

  transform: translateX(-50%);

  background: #fffdf9;

  border: 2px solid #231f2c;
  border-radius: 50% 50% 20% 20%;
}

/* FOOTER */

.footer {
  max-width: 1160px;
  min-height: 70px;

  margin: 0 auto;
  padding: 0 20px;

  display: flex;
  align-items: center;
  justify-content: space-between;

  color: #6b6577;
  font-size: 14px;
}

/* RESPONSIVE */

@media (max-width: 850px) {
  .header {
    height: auto;
    padding-top: 20px;
    padding-bottom: 20px;
  }

  .navigation {
    display: none;
  }

  .main-content {
    grid-template-columns: 1fr;
    gap: 40px;
    padding-top: 30px;
  }

  .presentation h1 {
    font-size: 48px;
  }

  .login-card {
    max-width: 500px;
  }

  .footer {
    flex-direction: column;
    gap: 10px;
    padding-top: 20px;
    padding-bottom: 20px;
  }
}

@media (max-width: 500px) {
  .presentation h1 {
    font-size: 40px;
  }

  .login-card {
    padding: 24px;
  }

  .password-container {
    flex-direction: column;
  }

  .show-password {
    width: 100%;
  }
}
</style>