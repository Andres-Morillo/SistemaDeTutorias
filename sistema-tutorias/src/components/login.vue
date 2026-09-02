<template>
  <div class="login-page">

    <!-- ================= HEADER ================= -->
    <header class="header">
      <div class="brand">

        <svg
          class="logo"
          width="56"
          height="56"
          viewBox="0 0 56 56"
          fill="none"
        >
          <rect
            x="1"
            y="1"
            width="54"
            height="54"
            rx="14"
            fill="#FFFDF9"
            stroke="#231F2C"
            stroke-width="2"
          />

          <circle cx="20" cy="24" r="9" fill="#6B9A55" />
          <circle cx="38" cy="22" r="8" fill="#E5B54C" />

          <path
            d="M8 48c0-9 6-15 14-15s14 6 14 15"
            fill="#6B9A55"
          />

          <path
            d="M32 48c0-8 5-13 12-13s12 5 12 13"
            fill="#E5B54C"
          />

          <path
            d="M42 10l1 3 3 1-3 1-1 3-1-3-3-1 3-1z"
            fill="#E5B54C"
          />
        </svg>

        <div class="brand-text">
          <p class="brand-name">Tuto Script</p>
          <p class="brand-description">
            Gestión de tutorías escolares
          </p>
        </div>

      </div>

      <nav class="navigation">
        <a href="#funciones">Funciones</a>
        <a href="#contacto">Contacto</a>
      </nav>
    </header>


    <!-- ================= CONTENIDO PRINCIPAL ================= -->
    <main class="main-content">

      <!-- --------- LADO IZQUIERDO --------- -->
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


        <!-- CARACTERÍSTICAS -->
        <div class="features">

          <div class="feature">
            <div class="feature-icon purple">
              <svg
                width="20"
                height="20"
                viewBox="0 0 24 24"
                fill="none"
                stroke="#231F2C"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <rect x="6" y="4" width="12" height="17" rx="2"/>
                <path d="M9 4V3a1 1 0 0 1 1-1h4a1 1 0 0 1 1 1v1"/>
                <path d="M9 12l2 2 4-4"/>
              </svg>
            </div>

            <span>Gestión de tutorías</span>
          </div>


          <div class="feature">
            <div class="feature-icon yellow">
              <svg
                width="20"
                height="20"
                viewBox="0 0 24 24"
                fill="none"
                stroke="#231F2C"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path d="M18 8a6 6 0 0 0-12 0c0 7-3 9-3 9h18s-3-2-3-9"/>
                <path d="M13.7 21a2 2 0 0 1-3.4 0"/>
              </svg>
            </div>

            <span>Notificaciones automáticas</span>
          </div>


          <div class="feature">
            <div class="feature-icon blue">
              <svg
                width="20"
                height="20"
                viewBox="0 0 24 24"
                fill="none"
                stroke="#231F2C"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path d="M3 3v18h18"/>
                <path d="M7 15l4-5 3 3 5-7"/>
              </svg>
            </div>

            <span>Seguimiento académico</span>
          </div>


          <div class="feature">
            <div class="feature-icon green">
              <svg
                width="20"
                height="20"
                viewBox="0 0 24 24"
                fill="none"
                stroke="#231F2C"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              >
                <path d="M14 3H7a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V8z"/>
                <path d="M14 3v5h5"/>
                <path d="M9.5 14.5h5M9.5 17.5h3.5"/>
              </svg>
            </div>

            <span>Reportes personalizados</span>
          </div>

        </div>

      </section>


      <!-- --------- LOGIN --------- -->
      <section class="login-card">

        <div class="login-header">
          <h2>Iniciar sesión</h2>

          <p>
            Ingresá tus datos para continuar
          </p>
        </div>


        <form @submit.prevent="handleSubmit">

          <!-- USUARIO -->
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
            />

          </div>


          <!-- CONTRASEÑA -->
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
              />

              <button
                type="button"
                class="show-password"
                @click="showPassword = !showPassword"
              >
                {{ showPassword ? 'Ocultar' : 'Mostrar' }}
              </button>

            </div>

          </div>


          <!-- OLVIDASTE CONTRASEÑA -->
          <a
            href="#"
            class="forgot-password"
            @click.prevent
          >
            ¿Olvidaste tu contraseña?
          </a>


          <!-- RECORDAR -->
          <label class="remember">

            <input
              v-model="form.remember"
              type="checkbox"
            />

            <span>Recordar usuario</span>

          </label>


          <!-- BOTÓN -->
          <button
            type="submit"
            class="login-button"
          >
            Iniciar sesión
            <span>→</span>
          </button>


          <!-- MENSAJE -->
          <p
            v-if="statusMessage"
            class="status-message"
          >
            {{ statusMessage }}
          </p>


          <div class="separator"></div>


          <!-- REGISTRO -->
          <p class="register">
            ¿No tenés cuenta?

            <a
              href="#"
              @click.prevent
            >
              Registrarse
            </a>
          </p>

        </form>

      </section>

    </main>


    <!-- ================= PAISAJE ================= -->
    <svg
      class="landscape"
      viewBox="0 0 1280 220"
      preserveAspectRatio="none"
      aria-hidden="true"
    >

      <path
        d="M0 172 Q 210 32 430 152 T 760 142 Q 950 97 1130 167 T 1280 157 V220 H0 Z"
        fill="#E7DDF6"
      />

      <path
        d="M0 170 Q 210 30 430 150 T 760 140 Q 950 95 1130 165 T 1280 155"
        fill="none"
        stroke="#231F2C"
        stroke-width="2.5"
      />

      <rect
        x="614"
        y="168"
        width="52"
        height="42"
        rx="6"
        fill="#FAEBC7"
        stroke="#231F2C"
        stroke-width="2"
      />

      <path
        d="
          M640 168
          C634 144 636 128 642 114
          M642 114
          C630 118 624 126 622 134
          M642 124
          C654 104 662 92 662 80
          M662 80
          C670 88 672 98 670 106
          M640 113
          C626 102 616 100 606 104
          M643 102
          C656 96 666 98 674 106
        "
        fill="none"
        stroke="#231F2C"
        stroke-width="2"
        stroke-linecap="round"
      />

    </svg>


    <!-- ================= FOOTER ================= -->
    <footer
      class="footer"
      id="contacto"
    >

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

/* =========================================================
   RESET
========================================================= */

:global(html),
:global(body),
:global(#app) {
  margin: 0;
  width: 100%;
  min-width: 1100px;
  min-height: 100%;
}

* {
  box-sizing: border-box;
}


/* =========================================================
   PÁGINA
========================================================= */

.login-page {
  width: 100%;
  min-width: 1100px;
  min-height: 100vh;

  background: #faf5ec;
  color: #231f2c;

  font-family:
    Arial,
    Helvetica,
    sans-serif;

  overflow-x: hidden;
}


/* =========================================================
   HEADER
========================================================= */

.header {
  width: 1160px;
  height: 92px;

  margin: 0 auto;

  display: flex;
  align-items: center;
  justify-content: space-between;
}

.brand {
  display: flex;
  align-items: center;
  gap: 16px;
}

.logo {
  flex-shrink: 0;
}

.brand-name {
  margin: 0;

  font-size: 26px;
  font-weight: 700;
  letter-spacing: -0.5px;
}

.brand-description {
  margin: 3px 0 0;

  font-size: 14px;
  color: #6b6577;
}

.navigation {
  display: flex;
  align-items: center;
  gap: 30px;
}

.navigation a {
  color: #6b6577;

  text-decoration: none;

  font-size: 14px;
  font-weight: 600;

  transition:
    color 0.2s ease;
}

.navigation a:hover {
  color: #231f2c;
}


/* =========================================================
   CONTENIDO
========================================================= */

.main-content {
  width: 1160px;

  margin: 0 auto;

  display: grid;
  grid-template-columns: 1fr 430px;

  column-gap: 90px;

  padding-top: 54px;
}


/* =========================================================
   PRESENTACIÓN
========================================================= */

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
  font-weight: 700;
}

.description {
  width: 520px;

  margin: 22px 0 0;

  color: #6b6577;

  font-size: 16px;
  line-height: 1.65;
}


/* =========================================================
   FEATURES
========================================================= */

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

  border: 1.5px solid #231f2c;

  border-radius: 50%;

  display: flex;
  align-items: center;
  justify-content: center;

  flex-shrink: 0;
}

.purple {
  background: #f4eefb;
}

.yellow {
  background: #faebc7;
}

.blue {
  background: #dceef9;
}

.green {
  background: #e2eeda;
}


/* =========================================================
   LOGIN CARD
========================================================= */

.login-card {
  width: 430px;

  background: #fffdf9;

  border: 2px solid #231f2c;

  border-radius: 24px;

  padding: 34px;
}


/* HEADER LOGIN */

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


/* =========================================================
   INPUTS
========================================================= */

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

  transition:
    box-shadow 0.15s ease,
    background 0.15s ease;
}

.input-group input:focus {
  background: #ffffff;

  box-shadow:
    0 0 0 3px rgba(78, 156, 212, 0.15);
}

.input-group input::placeholder {
  color: #aaa5ad;
}


/* =========================================================
   CONTRASEÑA
========================================================= */

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

  transition:
    background 0.15s ease;
}

.show-password:hover {
  background: #f4eee8;
}


/* =========================================================
   OLVIDASTE CONTRASEÑA
========================================================= */

.forgot-password {
  display: block;

  margin-top: 9px;

  text-align: right;

  color: #6b9a55;

  text-decoration: none;

  font-size: 13px;
  font-weight: 600;
}

.forgot-password:hover {
  text-decoration: underline;
}


/* =========================================================
   RECORDAR
========================================================= */

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

  cursor: pointer;
}


/* =========================================================
   BOTÓN LOGIN
========================================================= */

.login-button {
  width: 100%;
  height: 50px;

  margin-top: 24px;

  border: 1.5px solid #231f2c;

  border-radius: 10px;

  background: #4e9cd4;

  color: #231f2c;

  font-size: 15px;
  font-weight: 700;

  cursor: pointer;

  display: flex;
  align-items: center;
  justify-content: center;

  gap: 8px;

  transition:
    transform 0.12s ease,
    background 0.15s ease;
}

.login-button:hover {
  background: #61a9dd;

  transform: translateY(-1px);
}

.login-button:active {
  transform: translateY(0);
}

.login-button span {
  font-size: 19px;
}


/* =========================================================
   MENSAJE
========================================================= */

.status-message {
  margin: 12px 0 0;

  text-align: center;

  color: #6c4b9c;

  font-size: 13px;
  font-weight: 600;

  line-height: 1.4;
}


/* =========================================================
   SEPARADOR
========================================================= */

.separator {
  height: 1.5px;

  margin: 24px 0 17px;

  background: #e7e0d3;
}


/* =========================================================
   REGISTRO
========================================================= */

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

.register a:hover {
  text-decoration: underline;
}


/* =========================================================
   PAISAJE
========================================================= */

.landscape {
  display: block;

  width: 100%;
  height: 220px;

  margin-top: 55px;
}


/* =========================================================
   FOOTER
========================================================= */

.footer {
  width: 1160px;

  height: 70px;

  margin: 0 auto;

  display: flex;
  align-items: center;
  justify-content: space-between;

  color: #6b6577;

  font-size: 14px;
}

</style>