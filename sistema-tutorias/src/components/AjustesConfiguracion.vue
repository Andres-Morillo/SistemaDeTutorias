<template>
  <section class="settings-page" aria-labelledby="settings-title">
    <main class="settings-content">
      <header class="topbar">
        <div>
          <span class="eyebrow">Tu espacio de trabajo</span>
          <h1 id="settings-title">Configuración</h1>
          <p>Administrá tu cuenta y personalizá tu experiencia.</p>
        </div>
        <span class="heading-mark" aria-hidden="true">⚙</span>
      </header>

      <div class="settings-list">
        <section
          v-for="section in sections"
          :key="section.id"
          class="settings-card"
          :class="{ 'is-open': openSection === section.id }"
        >
          <button
            class="settings-trigger"
            type="button"
            :aria-expanded="openSection === section.id"
            :aria-controls="`${section.id}-panel`"
            @click="toggleSection(section.id)"
          >
            <span class="section-icon" :class="section.color" aria-hidden="true">
              {{ section.icon }}
            </span>
            <span class="section-copy">
              <strong>{{ section.title }}</strong>
              <small v-if="section.subtitle">{{ section.subtitle }}</small>
              <small v-else>{{ section.description }}</small>
            </span>
            <span class="chevron" aria-hidden="true">
              {{ openSection === section.id ? '−' : '+' }}
            </span>
          </button>

          <div
            v-if="openSection === section.id"
            :id="`${section.id}-panel`"
            class="settings-panel"
          >
            <form
              v-if="section.id === 'cuenta'"
              class="account-form"
              @submit.prevent="saveAccount"
            >
              <label class="field">
                <span>Nombre completo</span>
                <input v-model.trim="account.name" type="text" autocomplete="name" required />
              </label>
              <label class="field">
                <span>Correo electrónico</span>
                <input v-model.trim="account.email" type="email" autocomplete="email" required />
              </label>
              <label class="field">
                <span>Rol</span>
                <input :value="account.role" type="text" disabled />
              </label>
              <div class="panel-actions">
                <p v-if="accountMessage" class="feedback" role="status">{{ accountMessage }}</p>
                <button class="primary-button" type="submit">Guardar datos <span>→</span></button>
              </div>
            </form>

            <form
              v-else-if="section.id === 'contrasena'"
              class="account-form"
              @submit.prevent="changePassword"
            >
              <p class="demo-note">
                <strong>Modo de prueba:</strong> esta aplicación no cuenta con un servicio de
                autenticación para cambiar la contraseña. El formulario valida los campos, pero no
                modifica credenciales.
              </p>
              <label class="field full">
                <span>Contraseña actual</span>
                <input v-model="password.current" type="password" autocomplete="current-password" required />
              </label>
              <label class="field">
                <span>Nueva contraseña</span>
                <input v-model="password.next" type="password" autocomplete="new-password" minlength="8" required />
              </label>
              <label class="field">
                <span>Confirmar nueva contraseña</span>
                <input v-model="password.confirmation" type="password" autocomplete="new-password" minlength="8" required />
              </label>
              <div class="panel-actions">
                <p v-if="passwordMessage" class="feedback" role="status">{{ passwordMessage }}</p>
                <button class="primary-button" type="submit">Validar cambio <span>→</span></button>
              </div>
            </form>

            <div v-else-if="section.id === 'notificaciones'" class="panel-content">
              <label class="toggle-row">
                <span><strong>Notificaciones por correo</strong><small>Recibí avisos en {{ account.email || 'tu correo' }}.</small></span>
                <input v-model="notifications.email" type="checkbox" @change="saveNotifications" />
              </label>
              <label class="toggle-row">
                <span><strong>Recordatorios de tutorías</strong><small>Un aviso antes de tus próximas tutorías.</small></span>
                <input v-model="notifications.reminders" type="checkbox" @change="saveNotifications" />
              </label>
              <label class="field">
                <span>Horario para recibir avisos</span>
                <select v-model="notifications.deliveryTime" @change="saveNotifications">
                  <option value="anytime">A cualquier hora</option>
                  <option value="morning">Por la mañana (08:00–12:00)</option>
                  <option value="afternoon">Por la tarde (12:00–18:00)</option>
                </select>
              </label>
              <p class="demo-note">
                Estas opciones se guardan en este navegador; el envío de notificaciones aún no está
                conectado a un servicio.
              </p>
              <p class="feedback" role="status">{{ notificationMessage }}</p>
            </div>

            <div v-else-if="section.id === 'preferencias'" class="panel-content">
              <label class="field preference-field">
                <span>Idioma de la interfaz</span>
                <select v-model="preferences.language" @change="savePreferences">
                  <option value="es-AR">Español (Argentina)</option>
                  <option value="es">Español</option>
                </select>
              </label>
              <label class="toggle-row">
                <span><strong>Confirmar antes de cerrar sesión</strong><small>Mostrar un aviso antes de salir de tu cuenta.</small></span>
                <input v-model="preferences.confirmLogout" type="checkbox" @change="savePreferences" />
              </label>
              <p v-if="preferencesMessage" class="feedback" role="status">{{ preferencesMessage }}</p>
              <p class="demo-note">
                Estas preferencias se guardan en este navegador. La interfaz actualmente está
                disponible solo en español.
              </p>
            </div>

            <div v-else class="panel-content security-content">
              <div class="security-status">
                <span class="security-check" aria-hidden="true">✓</span>
                <span><strong>Sesión activa</strong><small>Conectado como {{ account.email || 'usuario' }}.</small></span>
              </div>
              <p class="demo-note">
                La verificación en dos pasos y el historial de accesos no están disponibles en este
                prototipo.
              </p>
              <button class="secondary-button" type="button" @click="requestLogout">
                Cerrar sesión en este dispositivo
              </button>
            </div>
          </div>
        </section>
      </div>
    </main>
  </section>
</template>

<script setup>
import { reactive, ref } from 'vue'

const props = defineProps({
  session: {
    type: Object,
    default: () => ({})
  }
})

const emit = defineEmits(['save-profile', 'logout'])

const openSection = ref('cuenta')
const account = reactive({
  name: props.session.name || '',
  email: props.session.email || '',
  role: props.session.role || 'docente'
})
const accountMessage = ref('')
const password = reactive({ current: '', next: '', confirmation: '' })
const passwordMessage = ref('')
const notificationMessage = ref('')
const preferencesMessage = ref('')

const sections = [
  { id: 'cuenta', title: 'Datos de la cuenta', subtitle: 'Editá tu información personal', icon: '♙', color: 'green' },
  { id: 'contrasena', title: 'Cambiar contraseña', subtitle: 'Actualizá tu contraseña de acceso', icon: '⚿', color: 'yellow' },
  { id: 'notificaciones', title: 'Notificaciones', subtitle: 'Personalizá cómo y cuándo recibir notificaciones', icon: '♧', color: 'blue' },
  { id: 'preferencias', title: 'Preferencias', description: 'Elegí cómo querés usar la aplicación', icon: '☷', color: 'purple' },
  { id: 'seguridad', title: 'Seguridad', description: 'Revisá el estado de tu sesión', icon: '◇', color: 'pink' }
]

const notifications = reactive(loadSettings('tutoScript.notifications', {
  email: true,
  reminders: true,
  deliveryTime: 'anytime'
}))
const preferences = reactive(loadSettings('tutoScript.preferences', {
  language: 'es-AR',
  confirmLogout: true
}))

function loadSettings(key, defaults) {
  try {
    const saved = localStorage.getItem(key)
    return saved ? { ...defaults, ...JSON.parse(saved) } : defaults
  } catch (error) {
    console.error(`No se pudieron cargar las preferencias (${key}).`, error)
    return defaults
  }
}

function toggleSection(id) {
  openSection.value = openSection.value === id ? '' : id
}

function saveAccount() {
  emit('save-profile', { name: account.name, email: account.email })
  accountMessage.value = 'Datos de la cuenta guardados.'
}

function changePassword() {
  passwordMessage.value = ''
  if (password.next !== password.confirmation) {
    passwordMessage.value = 'La nueva contraseña y la confirmación no coinciden.'
    return
  }
  if (password.next.length < 8) {
    passwordMessage.value = 'La nueva contraseña debe tener al menos 8 caracteres.'
    return
  }
  passwordMessage.value = 'Validación de prueba completada. No se modificaron credenciales.'
  password.current = ''
  password.next = ''
  password.confirmation = ''
}

function saveNotifications() {
  try {
    localStorage.setItem('tutoScript.notifications', JSON.stringify(notifications))
    notificationMessage.value = 'Preferencias de notificaciones guardadas en este navegador.'
  } catch (error) {
    console.error('No se pudieron guardar las preferencias de notificaciones.', error)
    notificationMessage.value = 'No se pudieron guardar las preferencias. Revisá el almacenamiento del navegador.'
  }
}

function savePreferences() {
  try {
    localStorage.setItem('tutoScript.preferences', JSON.stringify(preferences))
    preferencesMessage.value = 'Preferencias guardadas en este navegador.'
  } catch (error) {
    console.error('No se pudieron guardar las preferencias de la cuenta.', error)
    preferencesMessage.value = 'No se pudieron guardar las preferencias. Revisá el almacenamiento del navegador.'
  }
}

function requestLogout() {
  if (preferences.confirmLogout && !window.confirm('¿Querés cerrar la sesión en este dispositivo?')) {
    return
  }
  emit('logout')
}
</script>

<style scoped>
.settings-page {
  min-height: 100vh;
  margin: calc(-1 * clamp(1rem, 3vw, 2rem));
  padding: 38px clamp(20px, 5vw, 64px);
  background: #faf5ec;
  color: #231f2c;
  font-family: Arial, Helvetica, sans-serif;
}

.settings-content {
  width: min(100%, 1000px);
  margin: 0 auto;
}

.topbar {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 20px;
  padding-bottom: 24px;
  border-bottom: 1px solid rgb(35 31 44 / 14%);
}

.eyebrow {
  color: #6b9a55;
  font-size: 12px;
  font-weight: 700;
}

.topbar h1 {
  margin: 9px 0 5px;
  font-size: 32px;
  font-style: italic;
}

.topbar p {
  margin: 0;
  color: #6b6577;
  font-size: 14px;
}

.heading-mark {
  display: grid;
  width: 42px;
  height: 42px;
  flex: 0 0 42px;
  place-items: center;
  border: 1.5px solid #231f2c;
  border-radius: 50%;
  background: #faebc7;
  font-size: 21px;
}

.settings-list {
  display: grid;
  gap: 14px;
  margin-top: 24px;
}

.settings-card {
  overflow: hidden;
  border: 1.5px solid rgb(35 31 44 / 18%);
  border-radius: 15px;
  background: #fffdf9;
}

.settings-card.is-open {
  border-color: rgb(35 31 44 / 45%);
}

.settings-trigger {
  display: flex;
  width: 100%;
  min-height: 88px;
  align-items: center;
  gap: 15px;
  padding: 15px 20px;
  border: 0;
  background: transparent;
  color: inherit;
  text-align: left;
  font: inherit;
  cursor: pointer;
}

.settings-trigger:hover {
  background: #fffaf0;
}

.settings-trigger:focus-visible,
.primary-button:focus-visible,
.secondary-button:focus-visible {
  outline: 3px solid rgb(107 154 85 / 35%);
  outline-offset: -3px;
}

.section-icon {
  display: grid;
  width: 44px;
  height: 44px;
  flex: 0 0 44px;
  place-items: center;
  border: 1px solid rgb(35 31 44 / 20%);
  border-radius: 12px;
  font-size: 21px;
}

.section-icon.green {
  background: #e2eeda;
  color: #46733c;
}

.section-icon.yellow {
  background: #faebc7;
  color: #765915;
}

.section-icon.blue {
  background: #dceef9;
  color: #366d91;
}

.section-icon.purple {
  background: #f4eefb;
  color: #6c4b9c;
}

.section-icon.pink {
  background: #f6dfec;
  color: #8d4f70;
}

.section-copy {
  display: grid;
  flex: 1;
  gap: 5px;
}

.section-copy strong {
  font-size: 15px;
}

.section-copy small {
  color: #6b6577;
  font-size: 12px;
}

.chevron {
  color: #6b6577;
  font-size: 24px;
}

.settings-panel {
  padding: 0 22px 22px 79px;
  border-top: 1px solid rgb(35 31 44 / 10%);
}

.account-form,
.panel-content {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 16px;
  padding-top: 19px;
}

.field {
  display: flex;
  min-width: 0;
  flex-direction: column;
  gap: 7px;
}

.field > span {
  font-size: 12px;
  font-weight: 700;
}

.field input,
.field select {
  width: 100%;
  min-height: 42px;
  padding: 0 12px;
  border: 1px solid #c9c2b7;
  border-radius: 8px;
  background: #fffdf9;
  color: #231f2c;
  font: inherit;
  font-size: 13px;
}

.field input:focus,
.field select:focus {
  outline: 3px solid rgb(107 154 85 / 18%);
  border-color: #6b9a55;
}

.field input:disabled {
  background: #f4f0e8;
  color: #6b6577;
}

.field.full,
.panel-actions,
.demo-note,
.toggle-row,
.security-status,
.secondary-button {
  grid-column: 1 / -1;
}

.panel-actions {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 15px;
}

.feedback {
  margin: 0;
  color: #46733c;
  font-size: 12px;
  font-weight: 700;
}

.primary-button,
.secondary-button {
  min-height: 40px;
  padding: 0 15px;
  border: 1.5px solid #231f2c;
  border-radius: 9px;
  background: #e2eeda;
  color: #231f2c;
  font: inherit;
  font-size: 12px;
  font-weight: 700;
  cursor: pointer;
}

.primary-button span {
  margin-left: 5px;
  font-size: 17px;
}

.demo-note {
  margin: 0;
  padding: 11px 13px;
  border-radius: 8px;
  background: #fff5d8;
  color: #65562f;
  font-size: 12px;
  line-height: 1.5;
}

.toggle-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 18px;
  min-height: 54px;
  padding-bottom: 12px;
  border-bottom: 1px solid rgb(35 31 44 / 9%);
}

.toggle-row > span {
  display: grid;
  gap: 5px;
}

.toggle-row strong,
.security-status strong {
  font-size: 13px;
}

.toggle-row small,
.security-status small {
  color: #6b6577;
  font-size: 11px;
}

.toggle-row input {
  width: 18px;
  height: 18px;
  accent-color: #6b9a55;
  cursor: pointer;
}

.preference-field {
  grid-column: 1 / -1;
  max-width: 360px;
}

.security-status {
  display: flex;
  align-items: center;
  gap: 12px;
}

.security-status > span:last-child {
  display: grid;
  gap: 5px;
}

.security-check {
  display: grid;
  width: 36px;
  height: 36px;
  place-items: center;
  border-radius: 50%;
  background: #e2eeda;
  color: #46733c;
  font-weight: 700;
}

.secondary-button {
  justify-self: start;
  background: #fffdf9;
  color: #914d48;
}

.secondary-button:hover {
  background: #f6dfdc;
}

@media (max-width: 600px) {
  .settings-page {
    padding: 26px 16px;
  }

  .topbar h1 {
    font-size: 28px;
  }

  .settings-trigger {
    min-height: 78px;
    gap: 11px;
    padding: 13px;
  }

  .section-icon {
    width: 40px;
    height: 40px;
    flex-basis: 40px;
  }

  .settings-panel {
    padding: 0 14px 18px;
  }

  .account-form,
  .panel-content {
    grid-template-columns: 1fr;
  }

  .field.full,
  .panel-actions,
  .demo-note,
  .toggle-row,
  .security-status,
  .secondary-button,
  .preference-field {
    grid-column: auto;
  }

  .panel-actions {
    align-items: flex-start;
    flex-direction: column;
  }
}
</style>
