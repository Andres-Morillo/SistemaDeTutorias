<template>
  <div class="login-page">
    <header class="header">
      <a class="brand" href="#" aria-label="Tuto Script">
        <div class="logo" aria-hidden="true">
          <span class="logo-person purple"></span>
          <span class="logo-person yellow"></span>
        </div>

        <div class="brand-copy">
          <p class="brand-name">Tuto Script</p>
          <p class="brand-description">Gestión de tutorías escolares</p>
        </div>
      </a>

      <nav class="navigation" aria-label="Navegación principal">
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
          Organizá,<br />
          acompañá,<br />
          transformá.
        </h1>

        <p class="description">
          Tuto Script centraliza el seguimiento de tutorías escolares
          para que cada estudiante tenga el acompañamiento que necesita,
          sin planillas sueltas ni seguimientos perdidos.
        </p>

        <div class="features">
          <div
            v-for="feature in features"
            :key="feature.label"
            class="feature"
          >
            <div class="feature-icon" :class="feature.color" aria-hidden="true">
              {{ feature.icon }}
            </div>
            <span>{{ feature.label }}</span>
          </div>
        </div>
      </section>

      <section class="login-card" aria-labelledby="login-title">
        <template v-if="!isRecovery">
          <div class="login-header">
            <h2 id="login-title">Iniciar sesión</h2>
            <p>Ingresá tus datos para continuar</p>
          </div>

          <form @submit.prevent="handleSubmit" novalidate>
            <div class="input-group">
              <label for="email">Correo o usuario</label>
              <input
                id="email"
                v-model.trim="form.email"
                type="text"
                autocomplete="username"
                placeholder="nombre@escuela.edu.ar"
                required
              />
            </div>

            <div class="input-group">
              <label for="password">Contraseña</label>

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
                  :aria-label="showPassword ? 'Ocultar contraseña' : 'Mostrar contraseña'"
                  @click="showPassword = !showPassword"
                >
                  {{ showPassword ? 'Ocultar' : 'Mostrar' }}
                </button>
              </div>
            </div>

            <button
              type="button"
              class="forgot-password"
              @click="openRecovery"
            >
              ¿Olvidaste tu contraseña?
            </button>

            <label class="remember">
              <input v-model="form.remember" type="checkbox" />
              <span>Recordar usuario</span>
            </label>

            <button type="submit" class="login-button" :disabled="isSubmitting">
              <span>{{ isSubmitting ? 'Ingresando...' : 'Iniciar sesión' }}</span>
              <span aria-hidden="true">→</span>
            </button>

            <p v-if="statusMessage" class="status-message" role="status">
              {{ statusMessage }}
            </p>

            <div class="separator"></div>

            <p class="register">
              ¿No tenés cuenta?
              <button type="button" @click="solicitarRegistro">
                Registrarse
              </button>
            </p>
          </form>
        </template>

        <template v-else>
          <div class="login-header">
            <h2>Recuperar contraseña</h2>
            <p>Te guiamos para recuperar el acceso a tu cuenta</p>
          </div>

          <form v-if="recoveryStep === 'request'" @submit.prevent="sendRecovery" novalidate>
            <div class="input-group">
              <label for="recovery-email">Correo institucional</label>
              <input
                id="recovery-email"
                v-model.trim="recoveryEmail"
                type="email"
                autocomplete="email"
                placeholder="nombre@escuela.edu.ar"
                required
              />
            </div>

            <p class="recovery-label">Elegí cómo recibir las instrucciones</p>
            <div class="recovery-options" role="group" aria-label="Método de recuperación">
              <button
                type="button"
                class="recovery-option"
                :class="{ selected: recoveryMethod === 'codigo' }"
                :aria-pressed="recoveryMethod === 'codigo'"
                @click="recoveryMethod = 'codigo'"
              >
                <strong>Código</strong>
                <small>Ingresar un código de 4 dígitos</small>
              </button>
              <button
                type="button"
                class="recovery-option"
                :class="{ selected: recoveryMethod === 'enlace' }"
                :aria-pressed="recoveryMethod === 'enlace'"
                @click="recoveryMethod = 'enlace'"
              >
                <strong>Enlace</strong>
                <small>Abrir un enlace de recuperación</small>
              </button>
            </div>

            <p v-if="recoveryMessage" class="status-message" role="status">
              {{ recoveryMessage }}
            </p>

            <button type="submit" class="login-button">
              <span>Continuar</span><span aria-hidden="true">→</span>
            </button>
          </form>

          <div v-else-if="recoveryStep === 'verify'">
            <div class="recovery-note" role="status">
              <strong>Protocolo de prueba</strong>
              <span>En un sistema real enviaríamos este código a {{ recoveryEmail }}.</span>
              <span class="demo-code">{{ recoveryExpectedCode }}</span>
            </div>
            <form @submit.prevent="verifyRecoveryCode" novalidate>
              <div class="input-group">
                <label for="recovery-code">Código de verificación</label>
                <input
                  id="recovery-code"
                  v-model.trim="recoveryCodeInput"
                  type="text"
                  inputmode="numeric"
                  autocomplete="one-time-code"
                  maxlength="4"
                  placeholder="1234"
                  required
                />
              </div>
              <p v-if="recoveryMessage" class="status-message" role="status">
                {{ recoveryMessage }}
              </p>
              <button type="submit" class="login-button">
                <span>Verificar código</span><span aria-hidden="true">→</span>
              </button>
            </form>
          </div>

          <div v-else-if="recoveryStep === 'sent-link'" class="recovery-note" role="status">
            <strong>Enlace de prueba generado</strong>
            <span>En un sistema real enviaríamos el enlace a {{ recoveryEmail }}.</span>
            <a class="recovery-link" :href="recoveryUrl">Abrir enlace de recuperación</a>
          </div>

          <form v-else-if="recoveryStep === 'reset'" @submit.prevent="resetPassword" novalidate>
            <p class="recovery-note" role="status">
              {{ recoveryMethod === 'enlace' ? 'Enlace de prueba validado.' : 'Código verificado.' }} Elegí una contraseña nueva.
            </p>
            <div class="input-group">
              <label for="new-password">Nueva contraseña</label>
              <input id="new-password" v-model="newPassword" type="password" autocomplete="new-password" required />
            </div>
            <div class="input-group">
              <label for="confirm-new-password">Confirmar contraseña</label>
              <input id="confirm-new-password" v-model="confirmNewPassword" type="password" autocomplete="new-password" required />
            </div>
            <p v-if="recoveryMessage" class="status-message" role="status">
              {{ recoveryMessage }}
            </p>
            <button type="submit" class="login-button">
              <span>Restablecer contraseña</span><span aria-hidden="true">→</span>
            </button>
          </form>

          <div v-else class="recovery-note" role="status">
            <strong>Protocolo completado</strong>
            <span>La contraseña de prueba se restableció. Ya podés volver al acceso.</span>
          </div>

          <button type="button" class="recovery-back" @click="closeRecovery">
            ← Volver a iniciar sesión
          </button>
        </template>
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

const emit = defineEmits(['cambiar-a-registro', 'iniciar-sesion'])

const initialRecoveryParams = new URLSearchParams(window.location.search)
const hasRecoveryParams = initialRecoveryParams.get('flujo') === 'recuperar'
const recoveryToken = initialRecoveryParams.get('token') || ''
const recoveryExpectedCode = ref(initialRecoveryParams.get('codigo') || '1234')
const recoveryMethod = ref(initialRecoveryParams.get('metodo') || (recoveryToken ? 'enlace' : 'codigo'))
const recoveryStep = ref(
  recoveryToken ? 'reset' : initialRecoveryParams.has('codigo') ? 'verify' : 'request'
)
const isRecovery = ref(hasRecoveryParams)
const recoveryEmail = ref(initialRecoveryParams.get('email') || '')
const recoveryCodeInput = ref('')
const recoveryUrl = ref('')
const recoveryMessage = ref('')
const newPassword = ref('')
const confirmNewPassword = ref('')

const showPassword = ref(false)
const statusMessage = ref('')
const isSubmitting = ref(false)

const form = reactive({
  email: '',
  password: '',
  remember: false
})

const features = [
  {
    label: 'Gestión de tutorías',
    icon: '✓',
    color: 'purple'
  },
  {
    label: 'Notificaciones automáticas',
    icon: '!',
    color: 'yellow'
  },
  {
    label: 'Seguimiento académico',
    icon: '↗',
    color: 'blue'
  },
  {
    label: 'Reportes personalizados',
    icon: '▤',
    color: 'green'
  }
]

function handleSubmit() {
  statusMessage.value = ''

  if (!form.email || !form.password) {
    statusMessage.value = 'Completá tu usuario y contraseña para continuar.'
    return
  }

  isSubmitting.value = true

  window.setTimeout(() => {
    isSubmitting.value = false
    statusMessage.value = 'Sesión de prueba iniciada.'
    emit('iniciar-sesion', {
      email: form.email,
      name: form.email.includes('@') ? form.email.split('@')[0] : form.email
    })
  }, 500)
}

function openRecovery() {
  isRecovery.value = true
  recoveryEmail.value = form.email.includes('@') ? form.email : ''
  recoveryMessage.value = ''
}

function sendRecovery() {
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(recoveryEmail.value)) {
    recoveryMessage.value = 'Ingresá un correo válido para continuar.'
    return
  }

  recoveryMessage.value = ''
  const params = new URLSearchParams({
    flujo: 'recuperar',
    email: recoveryEmail.value,
    metodo: recoveryMethod.value
  })

  if (recoveryMethod.value === 'codigo') {
    recoveryExpectedCode.value = '1234'
    recoveryCodeInput.value = ''
    params.set('codigo', recoveryExpectedCode.value)
    recoveryStep.value = 'verify'
    return
  }

  const token = `prueba-${Date.now()}`
  params.set('token', token)
  recoveryUrl.value = `${window.location.origin}${window.location.pathname}?${params.toString()}`
  recoveryStep.value = 'sent-link'
}

function verifyRecoveryCode() {
  if (recoveryCodeInput.value !== recoveryExpectedCode.value) {
    recoveryMessage.value = 'El código no coincide. Revisá el código de prueba e intentá de nuevo.'
    return
  }
  recoveryMessage.value = ''
  recoveryStep.value = 'reset'
}

function resetPassword() {
  if (newPassword.value.length < 8) {
    recoveryMessage.value = 'La contraseña debe tener al menos 8 caracteres.'
    return
  }
  if (newPassword.value !== confirmNewPassword.value) {
    recoveryMessage.value = 'Las contraseñas no coinciden.'
    return
  }
  recoveryMessage.value = ''
  recoveryStep.value = 'complete'
  window.history.replaceState({}, document.title, window.location.pathname)
}

function closeRecovery() {
  isRecovery.value = false
  recoveryStep.value = 'request'
  recoveryMessage.value = ''
  window.history.replaceState({}, document.title, window.location.pathname)
}

function solicitarRegistro() {
  emit('cambiar-a-registro')
}
</script>

<style scoped>
:global(*) {
  box-sizing: border-box;
}

:global(html) {
  scroll-behavior: smooth;
}

:global(html),
:global(body),
:global(#app) {
  margin: 0;
  min-height: 100%;
}

:global(body) {
  background: #faf5ec;
}

button,
input {
  font: inherit;
}

.login-page {
  min-height: 100svh;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  overflow-x: hidden;
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}

/* HEADER */

.header {
  width: min(100%, 1160px);
  min-height: 92px;
  margin: 0 auto;
  padding: 18px 20px;

  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 24px;
}

.brand {
  display: inline-flex;
  align-items: center;
  gap: 14px;
  color: inherit;
  text-decoration: none;
}

.logo {
  position: relative;
  width: 52px;
  height: 52px;
  flex: 0 0 52px;

  display: flex;
  align-items: center;
  justify-content: center;

  border: 2px solid #231f2c;
  border-radius: 14px;
  background: #fffdf9;
  overflow: hidden;
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
  left: 50%;
  bottom: -13px;
  width: 25px;
  height: 14px;
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
  align-items: center;
  gap: 30px;
}

.navigation a {
  color: #6b6577;
  text-decoration: none;
  font-size: 14px;
  font-weight: 600;
  transition: color 160ms ease;
}

.navigation a:hover,
.navigation a:focus-visible {
  color: #231f2c;
}

/* MAIN */

.main-content {
  width: min(100%, 1160px);
  margin: 0 auto;
  padding: 54px 20px 0;

  display: grid;
  grid-template-columns: minmax(0, 1fr) minmax(320px, 430px);
  align-items: start;
  gap: clamp(40px, 7vw, 70px);
}

.presentation {
  min-width: 0;
  padding-top: 5px;
}

.eyebrow {
  display: inline-block;
  max-width: 100%;
  padding: 7px 15px;

  background: #f4eefb;
  border-radius: 999px;

  color: #6c4b9c;
  font-size: 13px;
  font-weight: 600;
}

.presentation h1 {
  margin: 20px 0 0;
  font-size: clamp(42px, 5.2vw, 62px);
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
  flex: 0 0 39px;

  display: flex;
  align-items: center;
  justify-content: center;

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
  outline: none;

  font-size: 15px;
  transition:
    border-color 160ms ease,
    box-shadow 160ms ease,
    background 160ms ease;
}

.input-group input:focus {
  background: #ffffff;
  border-color: #6b9a55;
  box-shadow: 0 0 0 3px rgb(107 154 85 / 12%);
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
  min-width: 0;
  flex: 1;
}

.show-password {
  height: 46px;
  flex: 0 0 auto;
  padding: 0 13px;

  border: 1.5px solid #231f2c;
  border-radius: 10px;

  background: #fffdf9;
  color: #231f2c;

  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}

.show-password:hover,
.show-password:focus-visible {
  background: #f4f0e8;
}

/* FORGOT */

.forgot-password {
  display: block;
  margin: 9px 0 0 auto;
  padding: 0;

  border: 0;
  background: transparent;

  color: #6b9a55;
  font-size: 13px;
  font-weight: 600;
  text-decoration: none;
  cursor: pointer;
}

.forgot-password:hover,
.forgot-password:focus-visible {
  text-decoration: underline;
}

.recovery-label {
  margin: 20px 0 0;
  font-size: 13px;
  font-weight: 700;
}

.recovery-options {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-top: 10px;
}

.recovery-option {
  min-height: 76px;
  padding: 11px;
  border: 1.5px solid #231f2c;
  border-radius: 10px;
  background: #fffdf9;
  color: #231f2c;
  text-align: left;
  cursor: pointer;
}

.recovery-option.selected {
  background: #e2eeda;
  box-shadow: 0 0 0 2px rgb(107 154 85 / 18%);
}

.recovery-option strong,
.recovery-option small {
  display: block;
}

.recovery-option small {
  margin-top: 5px;
  color: #6b6577;
  font-size: 11px;
  line-height: 1.35;
}

.recovery-note {
  display: grid;
  gap: 9px;
  margin-top: 20px;
  padding: 15px;
  border: 1.5px solid #231f2c;
  border-radius: 10px;
  background: #faebc7;
  color: #231f2c;
  font-size: 13px;
  line-height: 1.45;
}

.demo-code {
  justify-self: start;
  padding: 7px 12px;
  border: 1.5px solid #231f2c;
  border-radius: 8px;
  background: #fffdf9;
  font-size: 22px;
  font-weight: 700;
}

.recovery-link {
  color: #46733c;
  font-weight: 700;
  overflow-wrap: anywhere;
}

.recovery-back {
  display: block;
  margin: 20px auto 0;
  padding: 0;
  border: 0;
  background: transparent;
  color: #6b6577;
  font-size: 13px;
  font-weight: 600;
  cursor: pointer;
}

.recovery-back:hover,
.recovery-back:focus-visible {
  color: #231f2c;
  text-decoration: underline;
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
  margin: 0;
  accent-color: #6b9a55;
}

/* BUTTON */

.login-button {
  width: 100%;
  min-height: 50px;
  margin-top: 24px;
  padding: 0 18px;

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

  transition:
    transform 160ms ease,
    background 160ms ease,
    opacity 160ms ease;
}

.login-button:hover:not(:disabled),
.login-button:focus-visible {
  background: #61a9dc;
}

.login-button:active:not(:disabled) {
  transform: translateY(1px);
}

.login-button:disabled {
  opacity: 0.65;
  cursor: wait;
}

.login-button span:last-child {
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

.register button {
  padding: 0;
  border: 0;
  background: transparent;
  color: #4e9cd4;
  font-weight: 700;
  cursor: pointer;
}

.register button:hover,
.register button:focus-visible {
  text-decoration: underline;
}

/* LANDSCAPE */

.landscape {
  position: relative;
  width: 100%;
  height: 220px;
  margin-top: clamp(45px, 6vw, 55px);
  overflow: hidden;
  flex-shrink: 0;
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
  width: 52px;
  height: 42px;
  transform: translateX(-50%);

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
  width: min(100%, 1160px);
  min-height: 70px;
  margin: 0 auto;
  padding: 0 20px;

  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;

  color: #6b6577;
  font-size: 14px;
}

/* RESPONSIVE */

@media (max-width: 850px) {
  .header {
    min-height: auto;
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
    font-size: clamp(42px, 9vw, 52px);
  }

  .login-card {
    max-width: 500px;
  }

  .footer {
    flex-direction: column;
    justify-content: center;
    gap: 10px;
    padding-top: 20px;
    padding-bottom: 20px;
  }
}

@media (max-width: 500px) {
  .header {
    align-items: flex-start;
  }

  .brand-description {
    max-width: 190px;
  }

  .main-content {
    padding-left: 16px;
    padding-right: 16px;
  }

  .presentation h1 {
    font-size: 40px;
    letter-spacing: -1.5px;
  }

  .description {
    font-size: 15px;
  }

  .login-card {
    padding: 24px;
    border-radius: 20px;
  }

  .password-container {
    flex-direction: column;
  }

  .show-password {
    width: 100%;
  }

  .landscape {
    height: 180px;
  }

  .footer {
    padding-left: 16px;
    padding-right: 16px;
    font-size: 13px;
    text-align: center;
  }
}

@media (max-width: 360px) {
  .brand-name {
    font-size: 22px;
  }

  .brand-description {
    font-size: 12px;
  }

  .logo {
    width: 46px;
    height: 46px;
    flex-basis: 46px;
  }

  .presentation h1 {
    font-size: 36px;
  }

  .login-card {
    padding: 20px;
  }
}
</style>
